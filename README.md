<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>


<!-- PROJECT LOGO -->
<br />
<div align="center">
  
  <h3 align="center">Simulate Tail-Risk Scenarios</h3>

  <p align="center">
    A replication of key results from "Tail-GAN: Simulate Tail-Risk Scenarios"
    <br />
    <a href="https://colab.research.google.com/drive/1CQS6bMMrQ5OqciczWPHcZ134xQPiAdIY?usp=sharing">Open in Colab</a>
  </p>
</div>


<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
    </li>
    <li><a href="#license">License</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

Tail-GAN is a GAN architecture with the goal of training a generator to produce realistic stock return scenarios by implementing tail-sensitive loss functions.
This is achieved by projecting the stock scenarios to 1D PnL distributions from predictable strategies which in turn are evaluated based on their risk metrics (VaR and ES). 
While common loss functions (KL-Divergence, Wasserstein Distance) tend to oversample the center of the distribution, the proposed loss function is tail-sensitive.

### **Data**:

We reproduce the results for the synthetic data, which consists of $T = 100$ ticks per scenario with a realistic daily volatiliy. 
The paper uses a stochastic process of dimension 5 with each component having a different marginal probability distribution to simulate stock returns:

1. IID normal distribution
2. AR with $\psi_1 = 0.5$
3. AR with $\psi_2 = -0.5$
4. GARCH(1,1) with student-t ($\nu_1 = 5$) distribution
5. GARCH(1,1) with student-t ($\nu_2 = 10$) distribution

all of them are correlated by an underlying Gaussian innovation vector $u_t \sim \mathcal{N}(0,\Sigma)$ whose covariance matrix is constructed from a randomly drawn correlation matrix $\rho_{ij}$ and some scaling factors related to annualized standard deviations (i.e. a realistic volatility measure) normalized to the time frame that the series is supposed to represent.


### **Key Architecture Elements**

There are four important architectural features:

1.   A set of trading strategies which project the generated stock scenarios to PnL distributions. The discriminator takes these PnL distributions as input.
2.   The discriminator will output two values which will be interepreted as $\alpha$-VaR and $\alpha$-ES and handed as arguments to the score function. 
3.   The "Neural Sorting": This refers to a soft-sort algorithm by Grover et al (2019) which can be backpropagated through. Because $\alpha$-VaR of a distribution can be approximated by the $\lfloor \alpha n \rfloor^{\text{th}}$ smallest value in a sample of size $n$, sorting the PnL samples will potentially improve performance.
4.   The score function evaluates how close the discriminator is to the correct $\alpha$-VaR and $\alpha$-ES of the sampled distribution.

What is a "Score function"?

Consider a functional $T(F)$ mapping from a distribution $F$ to $\mathbb{R}$, e.g. the mean or VaR of a distribution. This functional is said to be elicitable if there exists a strictly proper scoring rule $S(x,y)$ s.t.:

$$ T(F) = \arg \min_{x \in \mathcal{X}} \mathbb{E}_{Y \sim F}[S(x,Y)]$$

In other words, $T(F)$ is elicitable if predicting $x = T(F)$ gives you the best expected score under the true data distribution $F$

VaR is elicitable but ES is not. But as it turns out the two are jointly elictable, i.e. we can find a score function $S(e,v,x)$ which is minimized for the correct VaR ($v$) and ES ($e$). 


### **Evaluating Generator Performance**

VaR and ES lend themselves to an interpretable measure of tail-realism. During training we can simply evaluate the difference between generated and real tail risk measures.
After training we can further:

- Compare tail risk measures on out-of-sample data
- Compare the correlation matrix of the generated data with that of the real data
- Compare autocorrelation coefficients accross the time-series with the real data
- Compare the rank-distributions of the generated and real data

These methods allow for a rigorous measure of model performance. 

<p align="right">(<a href="#readme-top">back to top</a>)</p>



### Built With

This project was built with [PyTorch](https://github.com/pytorch)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Getting Started

Simply install the required packages in your virtual environment and download and run the notebook.
Note that training of the models without a GPU can be quite slow.

<!-- LICENSE -->
## License

Not fixed yet.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

This project is a replication of the research by Cont et al. ("Tail-GAN: Learning to Simulate Tail Risk Scenarios"): [arXiv](https://arxiv.org/abs/2203.01664)

While I have added some numerical experiments and adapted hyperparameters as I saw fit, all of the key ideas in this project are theirs. 

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
[Next.js]: https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white
[Next-url]: https://nextjs.org/
[React.js]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://reactjs.org/
[Vue.js]: https://img.shields.io/badge/Vue.js-35495E?style=for-the-badge&logo=vuedotjs&logoColor=4FC08D
[Vue-url]: https://vuejs.org/
[Angular.io]: https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white
[Angular-url]: https://angular.io/
[Svelte.dev]: https://img.shields.io/badge/Svelte-4A4A55?style=for-the-badge&logo=svelte&logoColor=FF3E00
[Svelte-url]: https://svelte.dev/
[Laravel.com]: https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white
[Laravel-url]: https://laravel.com
[Bootstrap.com]: https://img.shields.io/badge/Bootstrap-563D7C?style=for-the-badge&logo=bootstrap&logoColor=white
[Bootstrap-url]: https://getbootstrap.com
[JQuery.com]: https://img.shields.io/badge/jQuery-0769AD?style=for-the-badge&logo=jquery&logoColor=white
[JQuery-url]: https://jquery.com 
