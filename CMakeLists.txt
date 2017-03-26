cmake_minimum_required(VERSION 2.8)
#====================================================================#
# Usage under Linux:                                                 #
#                                                                    #
#    From Marlin/buildroot/share/cmake folder:                       #
#        mkdir -p build && cd build                                  #
#        cmake ..                                                    #
#        make                                                        #
#                                                                    #
# Usage under Windows:                                               #
#                                                                    #
#    From Marlin/buildroot/share/cmake folder: :                     #
#        mkdir build && cd build                                     #
#        cmake -G"Unix Makefiles" ..                                 #
#        make                                                        #
#====================================================================#
 
#====================================================================#
# Download marlin-cmake scriptfiles if not already installed         #
# and add the path to the module path                                #
#====================================================================#

set(CMAKE_MODULE_PATH  ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/buildroot/share/cmake/modules)

#====================================================================#
#  Set included cmake files                                          #
#====================================================================#
include(Arduino_SDK) # Find the intallpath of Arduino SDK
include(marlin_cmake_functions)

#====================================================================#
#  Set toolchain file for arduino                                    #
#====================================================================#
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/buildroot/share/cmake/toolchain/ArduinoToolchain.cmake) # Arduino Toolchain

#====================================================================#
#  Setup Project                                                     #
#====================================================================#
project(Marlin C CXX)

#====================================================================#
#  Register non standard hardware                                    #
#====================================================================#
#register_hardware_platform(/home/tom/test/Sanguino)

#====================================================================#
#  Print any info                                                    #
#  print_board_list()                                                #
#  print_programmer_list()                                           #
#  print_board_settings(mega)                                        #
#====================================================================#
print_board_list()
print_programmer_list()


#====================================================================#
#  Get motherboard settings from Configuration.h                     #
#  setup_motherboard(TARGET Marlin_src_folder)                       #
#  Returns ${TARGET}_BOARD and ${TARGET}_CPU                         #
#                                                                    #
#  To set it manually:                                               #
#    set(${PROJECT_NAME}_BOARD mega)                                 #
#    set(${PROJECT_NAME}_CPU atmega2560)                             #
#====================================================================#
setup_motherboard(${PROJECT_NAME} ${CMAKE_CURRENT_LIST_DIR}/Marlin)

#====================================================================#
#  Setup all source files                                            #
#  Incude Marlin.ino to compile libs not included in *.cpp files     #
#====================================================================#

file(GLOB SOURCES "Marlin/*.cpp")
set(${PROJECT_NAME}_SRCS "${SOURCES};Marlin/Marlin.ino")

#====================================================================#
#  Define the port for uploading code to the Arduino                 #
#  Can be set from commandline with:                                 #
#    cmake .. -DUPLOAD_PORT=/dev/ttyACM0                             #
#====================================================================#
if(UPLOAD_PORT)
    set(${PROJECT_NAME}_PORT ${UPLOAD_PORT})
else()
    set(${PROJECT_NAME}_PORT /dev/ttyACM0)
endif()

#====================================================================#
#  Register arduino libraries not included in SDK                    #
#====================================================================#
#link_directories(/home/tom/test/ArduinoAddons) #U8glib
#set(${PROJECT_NAME}_ARDLIBS U8glib)
#set(U8glib_RECURSE True)

#====================================================================#
# Command to generate code arduino firmware (.hex file)              #
#====================================================================#
generate_arduino_firmware(${PROJECT_NAME})
