---
layout: post
title:  "VAE objectives for learning disentangled representations"
date:   2019-03-21 23:19:00 -0800
comments: true
categories: study  
---

In this post, I am summarizing variants of the original VAE objective. In particular, I am focusing on VAE variants designed to learn disentangled latent representations of data (disentangling factors of variation in the latent space). 

<!-- * [Various VAE objectivs](#VAE-objs)-->
* [ELBO - original VAE objective](#ELBO)
* [\\(\beta\\)-VAE](#betavae)
	* [VAE as a constrained optimization problem](#betavae_obj)
	* [Learning disentangled latent representations](#betavae_disentangle)
* [References](#ref)

<!-- ## <a name="VAE-objs"></a> Various VAE objectives -->
## <a name="ELBO"></a> ELBO - original VAE objective 
Lets start with the original VAE objective (ELBO: evidence lower bound in variational inference). 

$$
\begin{align}
\mathcal L(\theta, \phi, x^{(i)}) &= \mathbb E_{q_{\phi}(z | x^{(i)})} \left[\log p_{\theta} (x^{(i)}|z) \right] - D_{\text{KL}}\left(q_{\phi}(z|x^{(i)}||p_{\theta}(z)\right).
\end{align}
$$

The derivation of this objective (ELBO) can be found [here]({{ site.baseurl }}{% post_url 2019-02-27-VAE %}).

> For capturing factors of variations in a disentangled manner 

## <a name="betavae"></a> \\(\beta\\)-VAE 
### <a name="betavae_obj"></a> VAE as a constrained optimization problem
Instead of minimizing the original ELBO, where the sum of two terms are minimized at the same time, one can think of a constrained maximization problem, where the objective function is 

$$
\mathbb E_{q_{\phi}(z | x^{(i)})} \left[\log p_{\theta} (x^{(i)}|z) \right] \quad \text{subject to} \quad D_{\text{KL}}\left(q_{\phi}(z|x^{(i)}||p_{\theta}(z)\right) < \epsilon
$$


Remember that the first term in the ELBO \\(\mathbb E_{q_{\phi}(z \vert x^{(i)})} \left[\log p_{\theta} (x^{(i)}\vert z) \right]\\) can be interpreted as an expected reconstruction error, whereas the second term acts \\( D_{\text{KL}}\left(q_{\phi}(z\vert x^{(i)} \mid\mid p_{\theta}(z)\right) \\) as a regularizer. 

$$
\mathbb E_{q_{\phi}(z | x^{(i)})} \left[\log p_{\theta} (x^{(i)}|z) \right] -\beta D_{\text{KL}}\left(q_{\phi}(z|x^{(i)}||p_{\theta}(z)\right)
$$

With \\(\beta=1\\) the above objective function recovers the original VAE objective. 

### <a name="betavae_disentangle"></a> Learning disentangled latent representations
Lets assume that there are two sets of ground truth factors \\(v,w\\) that can be used to generate real-world images \\(x\\) such that \\(x \sim p(x \mid v,w)\\). Here, the one set of the factors \\(v\\) consists of conditionally independent factors and another set \\(w\\) consists of conditionally dependent factors. Since these ground truth factors are unknown, we can learn latent factors \\(z\\) only approximately from the observed data such that \\(p(x\mid z) \approx p(x \mid v,w)\\).

The goal is to learn representations of latent factors \\(z\\) to capture the conditionally independent ground truth factors \\(v\\) in a disentangled manner. One way to do this, as proposed in [[Higgins, et al](https://pdfs.semanticscholar.org/a902/26c41b79f8b06007609f39f82757073641e2.pdf)], is to introduce a constraint over the inferred posterior \\(q_{\phi}(z\mid x)\\) by trying to match it to a factorial prior, \\(p_{\theta}(z) = \prod_{\ell} p_{\theta}(z_\ell)\\), e.g., unit Gaussian \\(p_{\theta}(z) = \mathcal{N}(0, I)\\). 

Now going back to the objective function, we can choose the value of the paraemter, \\(\beta\\). Having small values of \\(\beta\\) puts more emphasis on the reconstruction error (the first term) and would results in higher reconstruction fidelty. On the other hand, higher values of \\(\beta > 1\\) would encourage learning disentangled representation as we put more emphasis on the constraint on the approximate posterior \\(q_{\phi}(z\mid x)\\), i.e., \\(q_{\phi}(z\mid x)\\) tries to match the factorial prior \\(p_{\theta}(z)\\). 

> Further dissection of VAE objectives

### VAE objectives with total-correlations

TBA


## <a name="ref"></a>  References
[1] Diederik P. Kingma, and Max Welling. ["Auto-encoding variational bayes.”](https://arxiv.org/abs/1312.6114) ICLR 2014.

[2] Danilo J. Rezende, Shakir Mohamed, Daan Wierstra. ["Stochastic Backpropagation and Approximate Inference in Deep Generative Models."](https://arxiv.org/abs/1401.4082) ICML 2014.

[3] Irina Higgins, Loic Matthey, Arka Pal, Christopher Burgess, Xavier Glorot, Matthew Botvinick, Shakir Mohamed, Alexander Lerchner. ["beta-VAE: Learning Basic Visual Concepts with a Constrained Variational Framework."](https://pdfs.semanticscholar.org/a902/26c41b79f8b06007609f39f82757073641e2.pdf), ICLR 2017.

{% if page.comments %}
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://klee44-github-io.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}


