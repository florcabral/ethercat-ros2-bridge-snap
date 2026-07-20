# ROS 2 Lyrical EtherCAT bridge snap

Prototype packaging for ROBENG-1886.

For the complete install, hardware, terminal, ROS topic, visualization, and
recording procedure, see [DEMO-STEPS.md](DEMO-STEPS.md) or
[DEMO-STEPS.html](DEMO-STEPS.html).

The snap bundles:

- ICube `ethercat_driver_ros2` from the pinned `jazzy` branch revision.
- IgH EtherCAT 1.6.9 userspace (`ecrt.h`, `libethercat`, and the `ethercat`
  CLI) from the pinned `stable-1.6` revision.
- ROS 2 control runtime dependencies not provided by the
  `ros-lyrical-ros-base` content snap.

The host provides the `ec_master` kernel module and `/dev/EtherCAT*`. No kernel
module or NIC driver is built into the snap.

## Demo result

The snap provides a one-command EasyCAT demo. It starts the EtherCAT ROS 2
Control bridge, maps the two analog inputs to ROS joint positions, publishes
`/joint_states`, and displays a live terminal dashboard:

  snap run ethercat-ros2-bridge.demo

The dashboard shows `A0` and `A1` as values from 0 to 255 with live bar graphs.
Its status changes to `LIVE` after complete data begins arriving. Bridge logs
are kept out of the presentation terminal and written to:

  ~/snap/ethercat-ros2-bridge/current/easycat-demo.log

## Build

The Lyrical extension is experimental in Snapcraft 9 and must be explicitly
enabled:

    SNAPCRAFT_ENABLE_EXPERIMENTAL_EXTENSIONS=1 snapcraft pack --use-lxd

## Install and connect the EtherCAT device

    sudo snap install --dangerous ./ethercat-ros2-bridge_0.1_amd64.snap
    sudo snap connect \
      ethercat-ros2-bridge:ros-lyrical-ros-base \
      ros-lyrical-ros-base:ros-lyrical-ros-base
    sudo snap connect \
      ethercat-ros2-bridge:ethercat-master \
      ethercat-ros2-bridge:ethercat-master-slot

The self-provided `custom-device` slot makes local strict-confinement testing
possible. Store publication and auto-connection require review because
`custom-device` is super-privileged.

The host must already have the IgH master and NIC driver loaded, expose
`/dev/EtherCAT0`, and use the validated EasyCAT receive timeout of 5000 µs.

## Basic terminal demo

Connect and power the EasyCAT device, then run:

  snap run ethercat-ros2-bridge.demo

Rotate both potentiometers. `A0` and `A1` should change independently. Stop the
demo with Ctrl-C.

## ROS 2 topic and visualization

The demo publishes `sensor_msgs/msg/JointState` on `/joint_states` with a fixed
order:

1. `easycat_analog_0`
2. `easycat_analog_1`

In another terminal, inspect the data or publication rate with:

  snap run ethercat-ros2-bridge.ros2 topic echo /joint_states
  snap run ethercat-ros2-bridge.ros2 topic hz /joint_states

For the recorded visualization demo:

1. Start `snap run ethercat-ros2-bridge.demo` in a terminal.
2. Start PlotJuggler with ROS 2 streaming on domain 0.
3. Plot `/joint_states/position[0]` and `/joint_states/position[1]` and label
   them A0 and A1.
4. Place the terminal, PlotJuggler, and a webcam view of the EasyCAT board on
   one screen.
5. Record the screen while rotating the two potentiometers.

Both applications use normal ROS 2 DDS networking, so no bridge process is
required between the snap and a host PlotJuggler installation.

## Diagnostic commands

With the host master running and exposing `/dev/EtherCAT0`:

    ethercat-ros2-bridge.ethercat version
    ethercat-ros2-bridge.ethercat slaves
    ethercat-ros2-bridge.ros2 pkg prefix ethercat_driver
    ethercat-ros2-bridge.ros2 pkg prefix ethercat_generic_slave

The expected slave identity is `Generic 32+32 bytes rev 1`.

If the dashboard reports `BRIDGE STOPPED` or `NO DATA`, inspect the demo log
above and check:

  snap connections ethercat-ros2-bridge
  ethercat-ros2-bridge.ethercat slaves
