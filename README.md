# **fpga-controls** #
This is a SystemVerilog solution to handle multi-direcitonal inputs in attempt to predict the player's intentions.

<p align="center">
<img src="https://i.imgur.com/DklV94U.png" width="350px" alt="MiSTer Enhanced Experience Logo">
</p>

**fpga-controls** is part of **MiSTer Enhanced Experience project** to aim to improve MiSTer experience.


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

2. Wire your inputs in your FPGA core. For example for a MiSTer core:
```systemverilog
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
    status[16:13] // [3:0] User Options. Check "[UIPD]" in ./diagonal_prediction.sv.
);
```

For more information, check "controls_top.sv". It provides **enhanced4wayjoy** and **enhanced2wayjoy** top level modules.

**Providing User Options**
=============
Module parameters are only intended for the developer as the system should always behave in one configuration. They should't be exposed to the user as it is unnecessary.

Users, however, have different preference on how their diagonal direction inputs should be handled. The whole point is to make the controls feel responsive in a preferred way, so they should not be forced to one option. Expose the **input [3:0] m_mode** of enhancedcontroller module.

**Notes**
=============
Long Live [MiSTer FPGA](https://github.com/MiSTer-devel/Main_MiSTer/wiki)!
