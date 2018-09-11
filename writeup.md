# Model Predictive Control Writeup

Daniel Hunter

## Model

As can be seen at the end of `FG_eval::operator()`, I use a kinematic vehicle model to plan the vehicle's trajectory. This ensures that the controller follows the laws of physics, otherwise it might find some local optima that does correlate to reality.

### State

The state returned from the simulator contains the current position of the vehicle
(x,y), the velocity, the angle, and the waypoints to be followed. The coefficients
of the polynomial best fitting the waypoints is determined, which is later sent
to the MPC solver. The state, containing the velocity, cross track error, and angle
error (position and angle are all zero from the cars perspective) is also created
and sent to the MPC solver.

### Actuators

The actuators used are simply the steering angle and the throttle -- as one might
expect. The MPC solver returns both the actuation commands (steering and throttle),
as well as the planned trajectory in the form of x,y points that can be plotted
on the simulator visually.

### Update Equations

Once the MPC solver has returned the result, the newly calculated ideal velocity
and steering commands are sent to the simulator for another cycle.

## Parameter Tuning

### Selection of N

I chose `N = 10` as the number of steps to plan out. On my system this seemed like
an ideal number -- I tried larger and smaller numbers (8,12,15) but this seemed
to give the best result. Too large of an N caused excessive CPU time, and too small
N caused jerky and undesirable driving patterns.

### Selection of dt

As the latency built into the simulator is 100ms, I set dt to also be 100ms.

## Waypoint Processing

To assist the polyfit, the waypoints were converted to be aligned along the x axis
as opposed to the y axis.

## Handling Latency

Due to the turning radius and velocity of the vehicle, latency was not a significant
issue. However, if the road was sharper or the latency longer, some processing
of the inputs may be needed, such as specifically looking ahead for tight turns or
stop signs (higher N, perhaps).
