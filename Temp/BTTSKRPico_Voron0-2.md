```RUBY
[include mainsail.cfg]
# This file contains common pin mappings for the BIGTREETECH SKR Pico V1.0
# To use this config, the firmware should be compiled for the RP2040 with
# USB communication.

# The "make flash" command does not work on the SKR Pico V1.0. Instead,
# after running "make", copy the generated "out/klipper.uf2" file
# to the mass storage device in RP2040 boot mode

## Voron Design VORON 0.2 SKR Pico V1.0 config

## *** THINGS TO CHANGE/CHECK: ***
## MCU path                                                                     [mcu] section
## Z and Extruder motor currents                                                [tmc2209 stepper_*] sections. Uncomment the stepper motor you have
## Full steps per rotation for Extruder                                         [extruder] section
## Thermistor types                                                             [extruder] and [heater_bed] sections - See https://www.klipper3d.org/Config_Reference.html#common-thermistors for common thermistor types
## Motor currents                                                               [extruder] [stepper] and [_HOME_X/Y] macro sections
## PID tune                                                                     [extruder] and [heater_bed] sections
## Fine tune E steps                                                            [extruder] section
## For more info                                                                check https://docs.vorondesign.com/build/startup/#v0

[mcu]
#####################################################################
# Obtain definition by "ls -l /dev/serial/by-id/"
#####################################################################
serial: /dev/serial/by-id/usb-Klipper_rp2040_REPLACE_WITH_YOUR_VALUE
## serial: /dev/ttyAMA0 											# for UART connection
restart_method: command


[temperature_sensor mcu_temp]
sensor_type: temperature_mcu
min_temp: 0
max_temp: 100

[temperature_sensor raspberry_pi]
sensor_type: temperature_host
min_temp: 10
max_temp: 100

[printer]
kinematics: corexy
max_velocity: 200
max_accel: 2000
max_z_velocity: 15
max_z_accel: 300
square_corner_velocity: 6.0

#####################################################################
#      X/Y Stepper Settings
#####################################################################

[stepper_x]
step_pin: gpio11
## Refer to https://docs.vorondesign.com/build/startup/#v0
dir_pin: gpio10                                                     # Check motor direction in link above. If inverted, add a ! before gpio10
enable_pin: !gpio12
rotation_distance: 40
microsteps: 32
full_steps_per_rotation: 200                                        # Set to 400 for 0.9° degree stepper motor, 200 is for 1.8° stepper motors
endstop_pin: tmc2209_stepper_x:virtual_endstop
position_endstop: 120
position_max: 120
homing_speed: 40
homing_retract_dist: 0
homing_positive_dir: true

[tmc2209 stepper_x]
uart_pin: gpio9
tx_pin: gpio8
uart_address: 0
interpolate: False
# run_current: 0.0            
# you need to calculate the run_current value using the equation (rated_motor_current * 0.707 = Maximum_run_current) start with a value that is about 60%-70% of your maximum value.
sense_resistor: 0.110
stealthchop_threshold: 0                                            # Set to 999999 to turn stealthchop on, and 0 to use spreadcycle
diag_pin: ^gpio4    												# YOU NEED TO JUMP THIS DIAG PIN ON YOUR BOARD FOR SENSORLESS HOMING TO WORK 
driver_SGTHRS: 255  												# this is set to 255 which is the MAX sensitivity for sensorless homing, you will need to tune this later

[stepper_y]
step_pin: gpio6
## Refer to https://docs.vorondesign.com/build/startup/#v0
dir_pin: gpio5                                                      # Check motor direction in link above. If inverted, add a ! before gpio5
enable_pin: !gpio7
rotation_distance: 40
microsteps: 32
full_steps_per_rotation: 200                                        # Set to 400 for 0.9° degree stepper motor, 200 is for 1.8° stepper motors
endstop_pin: tmc2209_stepper_y:virtual_endstop
position_endstop: 120
position_max: 120
homing_speed: 40
homing_retract_dist: 0
homing_positive_dir: true

[tmc2209 stepper_y]
uart_pin: gpio9
tx_pin: gpio8
uart_address: 2
interpolate: False
# run_current: 0.0            
# you need to calculate the run_current value using the equation (rated_motor_current * 0.707 = Maximum_run_current) start with a value that is about 60%-70% of your maximum value.
sense_resistor: 0.110
stealthchop_threshold: 0                                            # Set to 999999 to turn stealthchop on, and 0 to use spreadcycle
diag_pin: ^gpio3    												# YOU NEED TO JUMP THIS DIAG PIN ON YOUR BOARD FOR SENSORLESS HOMING TO WORK 
driver_SGTHRS: 255  												# this is set to 255 which is the MAX sensitivity for sensorless homing, you will need to tune this later

#####################################################################
#   Z Stepper Settings
#####################################################################

[stepper_z]
step_pin: gpio19
dir_pin: !gpio28                                                    # Remove the ! before gpio28 if motor direction is inverted.
enable_pin: !gpio2
rotation_distance: 8                                                # For T8x8 integrated lead screw
microsteps: 32
endstop_pin: ^gpio25
position_endstop: 120
position_max: 120
position_min: -1.5
homing_speed: 20
second_homing_speed: 3.0
homing_retract_dist: 3.0


[tmc2209 stepper_z]
uart_pin: gpio9
tx_pin: gpio8
uart_address: 1
interpolate: False
## For OMC (StepperOnline) 17LS13-0404E-200G 0.4A 
#run_current: 0.2
## For LDO-42STH25-1004CL200E 1.0A
#run_current: 0.37
sense_resistor: 0.110
stealthchop_threshold: 0                                            # Set to 999999 to turn stealthchop on, and 0 to use spreadcycle

#####################################################################
#   Extruder
#####################################################################

[extruder]
step_pin: gpio14
dir_pin: gpio13                                                     # Add ! if moving opposite direction
enable_pin: !gpio15
#full_steps_per_rotation: 200                                       # Set to 200 for LDO 1.8° stepper motor, and set to 400 for OMC(StepperOnline) 0.9° stepper motor
rotation_distance: 22.23                                            # See calibrating rotation_distance on extruders doc
gear_ratio: 50:10                                                   # For Mini Afterburner
microsteps: 32
nozzle_diameter: 0.400
filament_diameter: 1.750
heater_pin: gpio23
## Check what thermistor type you have. See https://www.klipper3d.org/Config_Reference.html#common-thermistors for common thermistor types.
## Use "Generic 3950" for NTC 100k 3950 thermistors
#sensor_type:
sensor_pin: gpio27
control: pid                                                        # Do PID calibration after initial checks
pid_Kp: 28.182
pid_Ki: 1.978
pid_Kd: 100.397
min_temp: 0
max_temp: 270
min_extrude_temp: 170
max_extrude_only_distance: 150
max_extrude_cross_section: 0.8
pressure_advance: 0.0                                               # See tuning pressure advance doc
pressure_advance_smooth_time: 0.040

[tmc2209 extruder]
uart_pin: gpio9
tx_pin: gpio8
uart_address: 3
interpolate: False
## For OMC (StepperOnline) 14HR07-1004VRN 1A 0.9°
#run_current: 0.5 # for OMC 14HR07-1004VRN rated at 1A
## For LDO LDO 36STH17-1004AHG 1A 1.8° 
#run_current: 0.3 # for LDO 36STH17-1004AHG
## For LDO LDO 36STH20-1004AHG 1A 1.8° 
#run_current: 0.6 # for LDO 36STH20-1004AHG
sense_resistor: 0.110
stealthchop_threshold: 0                                            # Set to 0 for spreadcycle, avoid using stealthchop on extruder

#####################################################################
#   Bed Heater
#####################################################################

[heater_bed]
heater_pin: gpio21
## Check what thermistor type you have. See https://www.klipper3d.org/Config_Reference.html#common-thermistors for common thermistor types.
## Use "Generic 3950" for Keenovo heaters
#sensor_type:
sensor_pin: gpio26
smooth_time: 3.0
#max_power: 0.6                                                     # Only needed for 100w pads
min_temp: 0
max_temp: 120
control: pid                                                        # Do PID calibration after initial checks
pid_kp: 68.453
pid_ki: 2.749
pid_kd: 426.122

#####################################################################
# Fan Control
#####################################################################

[heater_fan hotend_fan]
pin: gpio18
max_power: 1.0
kick_start_time: 0.5
heater: extruder
heater_temp: 50.0
#fan_speed: 1.0                                                     # You can't PWM the delta fan unless using blue wire

[fan]
pin: gpio17
max_power: 1.0
kick_start_time: 0.5                                                # Depending on your fan, you may need to increase this value if your fan will not start
off_below: 0.13
cycle_time: 0.010

[controller_fan MCU_fan]
pin: gpio20
max_power: 1.0
kick_start_time: 0.5
heater: extruder
#fan_speed: 1.0 

#####################################################################
#   Filament Runout Sensor
#####################################################################

#[filament_switch_sensor Filament_Runout_Sensor]
#pause_on_runout: True
#runout_gcode: PAUSE
#switch_pin: gpio16

#####################################################################
# Homing and Gantry Adjustment Routines
#####################################################################

[idle_timeout]
timeout: 1800

#[safe_z_home]
##home_xy_position: 120,120 										# these coordinates must NOT be greater than the values specified in "position_max:" for the X and Y steppers
#speed: 50.0
#z_hop: 5

## To be used with BED_SCREWS_ADJUST
[bed_screws]
screw1: 60,5
screw1_name: front screw
screw2: 5,115
screw2_name: back left
screw3: 115,115
screw3_name: back right

#####################################################################
# Neopixel
#####################################################################

[neopixel board_rgb]
pin: gpio24
chain_count: 1
color_order: GRB
initial_RED: 0.0
initial_GREEN: 0.1
initial_BLUE: 0.0

#####################################################################
#   V0 Display
#####################################################################
# [mcu display]
# serial: **PASTE YOUR SERIAL PORT HERE AND UNCOMMENT**
# restart_method: command

# [display]
# lcd_type: sh1106
# i2c_mcu: display
# i2c_bus: i2c1a
# # Set the direction of the encoder wheel
# #   Standard: Right (clockwise) scrolls down or increases values. Left (counter-clockwise scrolls up or decreases values.
# encoder_pins: ^display:PA3, ^display:PA4
# #   Reversed: Right (clockwise) scrolls up or decreases values. Left (counter-clockwise scrolls down or increases values.
# #encoder_pins: ^display:PA4, ^display:PA3
# click_pin: ^!display:PA1
# kill_pin: ^!display:PA5
# #x_offset: 2
# #   Use X offset to shift the display towards the right. Value can be 0 to 3
# #vcomh: 0
# #   Set the Vcomh value on SSD1306/SH1106 displays. This value is
# #   associated with a "smearing" effect on some OLED displays. The
# #   value may range from 0 to 63. Default is 0.
# #   Adjust this value if you get some vertical stripes on your display. (31 seems to be a good value)

# [neopixel displayStatus]
# pin: display:PA0
# chain_count: 1
# color_order: GRB
# initial_RED: 0.2
# initial_GREEN: 0.05
# initial_BLUE: 0

```
