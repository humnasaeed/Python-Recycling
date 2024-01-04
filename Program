ip_address = 'localhost' # Enter your IP Address here
project_identifier = 'P3B' # Enter the project identifier i.e. P3A or P3B

# SERVO TABLE CONFIGURATION
short_tower_angle = 315 # enter the value in degrees for the identification tower 
tall_tower_angle = 90 # enter the value in degrees for the classification tower
drop_tube_angle = 180 # enter the value in degrees for the drop tube. clockwise rotation from zero degrees

# BIN CONFIGURATION
# Configuration for the colors for the bins and the lines leading to those bins.
# Note: The line leading up to the bin will be the same color as the bin 

bin1_offset = 0.16 # offset in meters
bin1_color = [1,0,0] # e.g. [1,0,0] for red
bin1_metallic = False

bin2_offset = 0.15
bin2_color = [0,1,0]
bin2_metallic = False

bin3_offset = 0.15
bin3_color = [0,0,1]
bin3_metallic = False

bin4_offset = 0.11
bin4_color = [0.8,0,0.4]
bin4_metallic = False
#--------------------------------------------------------------------------------
import sys
sys.path.append('../')
from Common.simulation_project_library import *

hardware = False
if project_identifier == 'P3A':
    table_configuration = [short_tower_angle,tall_tower_angle,drop_tube_angle]
    configuration_information = [table_configuration, None] # Configuring just the table
    QLabs = configure_environment(project_identifier, ip_address, hardware,configuration_information).QLabs
    table = servo_table(ip_address,QLabs,table_configuration,hardware)
    arm = qarm(project_identifier,ip_address,QLabs,hardware)
else:
    table_configuration = [short_tower_angle,tall_tower_angle,drop_tube_angle]
    bin_configuration = [[bin1_offset,bin2_offset,bin3_offset,bin4_offset],[bin1_color,bin2_color,bin3_color,bin4_color],[bin1_metallic,bin2_metallic, bin3_metallic,bin4_metallic]]
    configuration_information = [table_configuration, bin_configuration]
    QLabs = configure_environment(project_identifier, ip_address, hardware,configuration_information).QLabs
    table = servo_table(ip_address,QLabs,table_configuration,hardware)
    arm = qarm(project_identifier,ip_address,QLabs,hardware)
    bot = qbot(0.1,ip_address,QLabs,project_identifier,hardware)

#--------------------------------------------------------------------------------
# STUDENT CODE BEGINS
#---------------------------------------------------------------------------------

def main():

    x = 1

    while x >= 1:
    
        import random

        #dispenses a random container from a list from (1,6)
        def dispense_container():
            random_int = random.randint(1,6)
            #Store all container properties in a list assined to a single variable  
            properties = table.dispense_container(random_int, True)


            #Extract materials, mass, and bin_number from the list 
            material = properties[0]
            global mass
            mass = properties[1]
            
            global bin_number
            bin_number = properties[2]

            print(mass, bin_number, material)

            #Return the destination bin number of the container
            return bin_number

        #loads container on to the qbot using the Qarm functions
        def load():
            arm.rotate_shoulder(40)
            time.sleep(2)
            arm.rotate_elbow(-20)
            time.sleep(2)
            arm.control_gripper(45)
            time.sleep(2)
            arm.rotate_elbow(-10)
            time.sleep(2)
            arm.rotate_base(-45)
            time.sleep(2)
            arm.rotate_shoulder(-40)
            time.sleep(2)
            arm.rotate_base(-45)
            time.sleep(2)
            arm.rotate_elbow(15)
            time.sleep(2)
            arm.rotate_shoulder(15)
            arm.control_gripper(-45)
            time.sleep(2)
            arm.rotate_elbow(-30)
            arm.home()

        dispense_container()

        load()

        #loads another dispensed container with the same bin id on the qbot aswell  
        def load_container():

            #Global variable to store the first container bin value in 
            global final_var
            final_var = bin_number 
            print("This is:", final_var)

            #Dispense container again and check bin number 

            for i in range (2):
                dispense_container() 

                print(bin_number)
           
                print(mass)
                #loads container on to the qbot using the Qarm function, and loads another dispensed container if the bin id is the same
                if mass <90 and bin_number == final_var:
                    #print(bin_number)
                    load()
                else:
                    break
            return final_var

        load_container()
           
        #Transfering container on Q-bot to the bins

        def transfer_container():


        
            print(final_var)

            #Converting the string bun number into an int as the variable y

            if final_var == "Bin01":

                y = 1

            elif final_var == "Bin02":

                y = 2

            elif final_var == "Bin03":

                y = 3

            elif final_var == "Bin04":

                y = 4
       
            print(y)

            #Calculate value to obtain the according colour from a list.
            x = y - 1
       


           #activate all required sensors
            bot.activate_line_following_sensor()
            bot.activate_color_sensor()


            #A list of all possible bin colour values. 

            colour_values =[[1,0,0],[0,1,0],[0,0,1],[0.8,0,0.4]]

            #print(bin_number)

            #Moving the bot using the line following sensor 


            while bot.read_color_sensor()[0] != colour_values[x]:
                print(bot.read_color_sensor()[0])

                if bot.line_following_sensors() == [1,1]:
                    bot.set_wheel_speed([0.04,0.04])

                elif bot.line_following_sensors() == [1,0]:
                    bot.set_wheel_speed([0.007,0.02])

                elif bot.line_following_sensors() == [0,1]:
                    bot.set_wheel_speed([0.02, 0.007])

            else:

                #If sensor detects colour then the qbot will stop after a specific time.
                
                forward_time = time.time()
                print(forward_time)

                #forward time initializes the time to zero.  

                while time.time() < (forward_time + 8.7):
                    print(forward_time + 5)
                    if bot.line_following_sensors() == [1,1]:
                        bot.set_wheel_speed([0.07,0.07])

                    elif bot.line_following_sensors() == [1,0]:
                        bot.set_wheel_speed([0.007,0.02])

                    elif bot.line_following_sensors() == [0,1]:
                        bot.set_wheel_speed([0.02, 0.007])
                else:
                    #Qbot will stop at the bin. 
                    bot.stop()
                    
                time_elapsed = time.time() - forward_time
                print(time_elapsed) 

        transfer_container()

        #Deposits container into the bin
        def deposit_container():
            bot.activate_linear_actuator()
            bot.rotate_hopper(50)
            time.sleep(2)
            bot.rotate_hopper(-50)
            time.sleep(2)
            #bot.forward_distance(0.15)
            #time.sleep(2)
            #bot.rotate(95)
            bot.deactivate_linear_actuator()
            return
       
        deposit_container()


        #Bot returns home to same postion as the starting point in order to repeat the cycle
        def return_home():

            #The bot will follow the line until it reads the position 1.45

            while float(round(bot.position()[0],2)) != 1.45:
                print(round(bot.position()[0],2))

                if bot.line_following_sensors() == [1,1]:
                    bot.set_wheel_speed([0.07,0.07])

                elif bot.line_following_sensors() == [1,0]:
                    bot.set_wheel_speed([0.007,0.02])

                elif bot.line_following_sensors() == [0,1]:
                    bot.set_wheel_speed([0.02, 0.007])

            else:

                #Qbot will countiue to move prior to fully stopping at the home position. 

                forward_time = time.time()
                print(forward_time)

                #forward time initializes to zero. 

                while time.time() < (forward_time + 2):
                    #Bot moves forward for 2 seconds to reach home position. 
                    if bot.line_following_sensors() == [1,1]:
                        bot.set_wheel_speed([0.07,0.07])

                    elif bot.line_following_sensors() == [1,0]:
                        bot.set_wheel_speed([0.007,0.02])

                    elif bot.line_following_sensors() == [0,1]:
                        bot.set_wheel_speed([0.02, 0.007])
                else:
                    #Qbot stops at home position
                    bot.stop()

        #Call return _home() function
        return_home()
        #Increment x by one to indicate the number of repitions in the cycle. 
        x =+1
        
#Calling the main function 
main()

#---------------------------------------------------------------------------------
# STUDENT CODE ENDS
#---------------------------------------------------------------------------------
    

    

