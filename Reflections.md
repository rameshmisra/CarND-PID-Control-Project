# Reflections on PID project

I found it interesting to revisit the race course we had trained a car to navigate using a neural network in term 1. Training the neural net required a considerable amount of data, manipulation (and potentially augmentation), and of course reasonably powerful computing resources (for training). While the inference part, did not necessarily require considerable computing resources, I would expect that in a real world application, where the autonomous vehicle should be able to learn from the human driver in special/ unique situations and this would need a powerful compute engine on-board.

Compared to the neural network approach, the PID controller needs insignificant compute power. However, what we get is a pretty limited controller, as discussed below. Its key advantage is its simplicity.

## PID coefficient values & their determination

I used a manual tuning approach, which was time consuming and needed careful attention, even though just 3 coefficients were being tweaked. For the code, I relied considerably on the project instructional video on YouTube. I conceptually understand the "Twiddle" approach, but felt handicapped in my C++ skills to implement it, especially as the code is talking to the simulator. With just 3 parameters, I expect the computation for optimizing the P-I-D coefficients should be pretty straightforward, and the hypersurface is not likely to be too convoluted to worry about local vs. global minima. However, I noticed that the *coefficients need to be different for different speeds/ throttle values*, so it becomes a 4-D hyperplane.

For the default/ initial throttle value of 0.3 (which results in a speed of ~30mph), these were the coefficients I deduced:
## Final coefficients for throttle = 0.3:  Kp = -0.07; Ki = -0.005, Kd = -2.1
With these settings, the car could loop through the course multiple times, without getting off track.

At different speeds/ throttle values, these coefficients change! This matches human intuition: at lower speeds steering angle needs to be greater to achieve the same change in heading compared to higher speeds. For a throttle value of 0.5 (max speed ~50mph), the best coefficient values I came up with are: -0.012, -0.007, -0.997 respectively. I have uploaded a video of one loop through the course; while I'd hate to be the passenger in *that* car, it pretty much goes around the course without significant "off-roading". At higher throttle values, I was unable to prevent the car from going off-roading; it may be possible, but I believe a PID control for modulating the throttle would be required.

## Impact of individual coefficient values
The P coefficient's effect is pretty easy to see; larger (negative) values makes the car turn quicker to offset/reduce the CTE, though increasing the chance of overshoot. A key point from driving lessons many years ago of keeping a "light touch" (= small magnitude), apply here too, and at higher speeds the P coefficient needs to be smaller than the corresponding value for lower speed driving.
The I coefficient had a subtle impact. Given that the race track had a bias to turning left, I realized this was similar to a misalignment issue in a car. Keeping it at a small negative value, was required to navigate this course. Flipping it to a positive value resulted in the car quite quickly veering off to the right. In a real world scenario, in right-hand-drive geographies considering typical road camber, I suspect the I coefficient would need to be non-zero & slightly negative. However as mentioned above, we would need different values of I for different speeds.
The D coefficient tended to dampen the steering oscillation, when chosen "correctly"; if the magnitude was too far off from optimal, it could increase the steering oscillation and veer the car off-course quite rapidly. Like the other coefficients, this also needed to have a lower magnitude at higher speeds.

## Key take-away from this project
PID control is a pretty simple and easy to implement control strategy for cars. However, a certain set of values may have a very narrow range of applicability: drive at a different speed, we need a new set of PID coefficients; one of the tires is under-inflated or over inflated, the old PID values (especially I) could get the autonomous vehicle into a ditch. I suspect that differences in road surfaces and traction (wet vs. dry) will need modified PID values.
As a control strategy for an autonomous vehicle, while a purely neural network based approach may work (with adequate data), in reality where we will always have only a finite set of data perhaps some sort of a hybrid control strategy (PID + NN) could be most effective ( or at least a situation where the PID control is the zeroth order element).
