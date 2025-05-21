# ICS-BatchNorm
### **Interactive visualization simulating how Gamma and Beta control ICS for faster convergence in BatchNorm:**

<div align="center">

![Interactive Simulation](./Intercative%20simulation.jpeg)

</div>

</div>


**_Affine Transformation:_**  
After we normalize our pre-activations, we pass them through a linear transformation:

<div align="center">

![Affine Transformation Formula](./affine%20tranform.png)

</div>



  
We initialize gamma to 1 and beta to 0 in BatchNorm to ensure that the model begins training using the pure normalized output (zero mean and unit variance) without any learned distortion. By starting from this neutral configuration, the model can then learn, through gradient descent, how to adjust gamma and beta to scale and shift the activations in a way that improves performance. This approach preserves the benefits of normalization early on while allowing the network to later modify the distribution of activations as needed to enhance expressiveness.

**_So, what Gamma and Beta actually do?_**  
Geometrically speaking, variance influences the speed of our gradients across the loss landscape, while the mean guides their direction. Imposing unit variance through normalization can sometimes be overly restrictive, dampening the model's ability to make decisive moves toward the minima. Similarly, forcing a zero mean may misalign the gradient flow, especially in deeper layers where the network grows increasingly confident in its learned representations. As signals propagate deeper, the model begins to favor certain directions, and gradients reflect this bias. To accommodate and even encourage this behavior, we must allow the network to set a mean that aligns with its trajectory. The affine transformation step in Batch Normalization resolves these constraints: gamma scales the normalized activations to regulate gradient magnitude, while beta shifts them to guide the model's focus, effectively restoring the network's agency to navigate the loss landscape with both speed and intent.
> *Feel free to tinker with the simulation and visualize how gamma and beta actually influence gradients and help reduce the loss.*


**_Covariate Shift and how does it link to the affine transformation?_**  
If you ever came across the original paper of BatchNorm, you'll notice that the main thing authors claim to address with their algorithm is the Internal Covariate Shift.  

ICS is simply the continuous change in the network's activations distribution as parameters keep getting updated, exactly the downside we highlighted earlier about initialization techniques. The truth is, ICS is not significantly reduced or mitigated at all, and that is not a bad thing as many people think. In fact, it is actually healthy for deep neural nets.  

To understand how and why, all we have to do is link the dots. We saw that a unit variance is good keeping activations in check, but the model might need a bit more freedom to learn all the features. This is when Gamma and Beta come into the picture, and they're learned parameters for a reason:  

Initially, they're set to one and zero respectively to ensure activations will be purely sampled from a zero mean and unit variance distribution. Then, in the backward pass, we compute how the loss changes with respect to Gamma and Beta which mirrors how much does the normalization impact the loss in the first training step. The gradients with respect to γ and β reflect how much the network wants to stretch or shift the normalized activations. In effect, the model sculpts its own input distribution for each layer, not to suppress ICS, but to control and leverage it. And the same workflow persists at every single layer, every single mini-batch, and every single epoch.  

**In conclusion, we can all agree that a moderate and controllable ICS is in fact lucrative and helps keep our activations stable while allowing informed statistical calibrations for faster convergence.**
