# **fpga-controls** #
This is a SystemVerilog solution to handle multi-direcitonal inputs in attempt to predict the player's intentions.

<p align="center">
<img src="https://i.imgur.com/DklV94U.png" width="350px" alt="MiSTer Enhanced Experience Logo">
</p>

**fpga-controls** is part of the **MiSTer Enhanced Experience project** aimed at improving MiSTer experience.

A lot of systems and software are designed to work best on the controller shipped for them. As most users use their favorite controllers to play them on FPGA (like the awesome **MiSTer FPGA** system), more capable controllers are able to send inputs that the original systems were never prepared for. The biggest example is playing 4-way-only joystick systems on a dpad or arcade sticks with a 8-way gate.

Diagonal inputs can easily be pressed by accident when you're quickly changing directions. This makes it quite difficult to play games shipped with 2-way or 4-way only controllers.

**Features**
=============
- Horizontal & Vertical **Simultaneous Opposite Cardinal Direction (SOCD)** Cleaner
- User Intent Prediction for Diagonal Inputs
- Optimal Behavior for Perfect Diagonal Inputs
- Control Override for 2/4-way-only Systems

**Implementation**
=============
You can use the indiviual modules if you just want one small part of the solution, but for a proper implementation simply use the enhancedcontroller module in controls_top.sv.

Implement this goodie in just two steps!

1. Include controls_top.sv.

2. Wire your inputs in your FPGA core. 

**[4-Way]** For example for a MiSTer core:
```systemverilog
//  parameters:
//  1. What to do when both Up & Down switches are on: FAVOR_ZERO, FAVOR_UP, FAVOR_DOWN
//  2. What to do when both Left & Right switches are on: FAVOR_ZERO, FAVOR_LEFT, FAVOR_RIGHT
//  3. Favored direction on pure diagonal inputs: DIR_HORIZONTAL, DIR_VERTICAL
//  For more information, check out "controls_top.sv"
enhanced4wayjoy #(FAVOR_ZERO, FAVOR_ZERO, DIR_HORIZONTAL) player1
(
    clk_sys,
    {
        // p1_btn_x: keyboard, joy[x]: game pads
        p1_btn_up    | joy[3],
        p1_btn_down  | joy[2],
        p1_btn_left  | joy[1],
        p1_btn_right | joy[0]
    },
    {m_p1_up, m_p1_down, m_p1_left, m_p1_right}, // Output wire to the core
    status[16:13] // 4bit User Options. Check "[UIPD]" in ./diagonal.sv.
);
```

For more information, check "controls_top.sv". It provides **enhanced4wayjoy** and **enhanced2wayjoy** top level modules.

## Available User Options

**All Systems**
* **None** - Does not do anything. Unpredictable outcome.
* **No Movement** - This "Clear" option assumes the diagonal input is an error, and both input must be stopped to prevent any potential disasters. Some prefer this for games like Tetris.
* **Vertical** - Always forces* to vertical directions.
* **Horizontal** - Always forces* to horizontal directions.

**4-Way-Only**
* **New Direction** - Otherwise known as "Prediction", when diagonal input is received, it assumes the new direction is intended so the direction that was pressed later than the earlier direction is processed. This is the most preferred way and recommended for most systems.
* **Old Direction** - The often called "Correction" is a way to respect the earlier direction and correct it to stay on course while ignoring the new direction in the diagonal input.

**Forcing into one direction like this cannot prevent any movements if the opposing movement was pressed first (as we cannot predict future). It still prevents the other direction being pressed further.*

**2-Way-Only systems will only have one over the other of the two*

**Providing User Options**
=============
Module parameters are only intended for the developer as the system should always behave in one configuration. They should't be exposed to the user as it is unnecessary.

Users, however, have different preference on how their diagonal direction inputs should be handled. The whole point is to make the controls feel responsive in a preferred way, so they should not be forced to one option. Expose the **input [3:0] m_mode** of enhanced2wayjoy/enhanced4wayjoy module.

**Notes**
=============
Long live [MiSTer FPGA](https://github.com/MiSTer-devel/Main_MiSTer/wiki)!
