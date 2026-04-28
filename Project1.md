---
layout: default
title: "Project 1"
permalink: /projects/project-1/
markdown: kramdown
---

# Likelihood approach to changepoint detection + logarithmic data example

## The general idea
	
Let the underlying model be given by
	
\\[g(z) = \\begin{cases}
	g_{1}(z), & \min(z) \leq z \leq \theta_{1}, \newline
	g_{2}(z), & \theta_{1} < z \leq \theta_{2}, \newline
	\vdots \newline
	g_{n}(z), & \theta_{n-1} < z \leq \max(z).
\end{cases}\\]
	
Let the noise be given by
	
\\[\boldsymbol{\epsilon} = \begin{bmatrix}
	\epsilon_{1} \newline
	\epsilon_{2} \newline
	\vdots \newline
	\epsilon_{n}
\end{bmatrix}\\]
	
where $\epsilon_{i} \sim \mathcal{F}$, a probability distribution with mean 0.
	
Let the covariates be given by
	
\\[\boldsymbol{Z} = \begin{bmatrix}
	z_{1} \newline
	z_{2} \newline
	\vdots \newline
	z_{n}
\end{bmatrix}\\]
	
Then 
	
\\[\boldsymbol{Y} = g(\boldsymbol{Z}) + \boldsymbol{\epsilon}\\]
	
This allows us to write the density of $\mathcal{F}$ in terms of the covariates $z_{i}$, from which the likelihood can be derived. This can then be maximised to find $\boldsymbol{\theta}$ as well as other model parameters.
	
## An example
	
Let 
	
\\[g(z) = \begin{cases}
	\log\left(\frac{z+a}{a}\right), 				  & 0 \leq z \leq \theta, \newline
	\log\left(\frac{(z+a)(z+a-\theta)}{a^{2}}\right), & z > \theta.
\end{cases}\\]
	
where $a > 0$, $0 < \theta < \max(\boldsymbol{Z})$.
	
Let $\mathcal{F} = N(0, \sigma^{2})$, the normal distribution.
	
Then $\boldsymbol{Y} = f(\boldsymbol{Z}) + \boldsymbol{\epsilon}$. This is the same as writing $y_{i} = N(f(z_{i}), \sigma^{2})$. This allows us to write the density function of the normal distribution as
	
\\[f_{\mathcal{F}}(y_{i}, z_{i}) = \frac{1}{\sigma\sqrt{2\pi}}e^{\frac{\left(y_{i} - g(z_{i})\right)^{2}}{2\sigma^{2}}}\\]
	
Therefore, the likelihood function is given by
	
\\[\begin{aligned}
	L(\boldsymbol{\theta}, a, \sigma^{2}; \boldsymbol{Y}) &= \prod_{i=1}^{n}f_{\mathcal{F}}(y_{i}, z_{i}; \boldsymbol{\theta}, a, \sigma^{2}) \newline
		&= \left(\frac{1}{\sigma\sqrt{2\pi}}\right)^{n}\prod_{i=1}^{n}e^{\frac{\left(y_{i} - g(z_{i})\right)^{2}}{2\sigma^{2}}}
\end{aligned}\\]
	
So the log-likelihood is given by
	
\\[\begin{aligned}
	l(\boldsymbol{\theta}, a, \sigma^{2}; \boldsymbol{Y}) &= n\log\left(\frac{1}{\sigma\sqrt{2\pi}}\right) + \sum_{i=1}^{n}\frac{\left(y_{i} - g(z_{i})\right)^{2}}{2\sigma^{2}} \newline
		&= n\log\left(\frac{1}{\sigma\sqrt{2\pi}}\right) + \frac{1}{2\sigma^{2}}\sum_{i=1}^{n}\left(y_{i} - g(z_{i})\right)^{2} 
\end{aligned}\\]
	
This can then be numerically maximised to find $\theta$. An example is below

<div style="text-align: center;">
	<img src="/assets/images/dataImage.png" alt="dataImage" width="400">
</div>

The function used to generate this data was

For this data, the $\boldsymbol{X}$ values used were
	
\\[\boldsymbol{X} = \begin{bmatrix}
	0 \newline
	1 \newline
	\vdots \newline
	1000
\end{bmatrix}\\]

The true and estimated values to 4 d.p. for this model are below

<div style="text-align: center; margin: 1em 0;">
  <table style="margin: 0 auto; border-collapse: collapse; width: auto;">
    <thead>
      <tr>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">Parameter</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">True value</th>
        <th style="border: 1px solid #ccc; padding: 6px 12px;">Estimate</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">a</td>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">1</td>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">0.9962</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">$\sigma$</td>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">0.5</td>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">0.5004</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">$\theta$</td>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">400</td>
        <td style="border: 1px solid #ccc; padding: 6px 12px;">400.1035</td>
      </tr>
    </tbody>
  </table>
</div>
	
Clearly this is very accurate for these parameters and underlying model