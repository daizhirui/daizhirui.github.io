---
layout: article
show_date: false
---

# Making UC San Diego Snowy Again

In this project, a model based on cycleGAN and attention is proposed. It can be applied to scene transformation, for example, modifying a image of UC San Diego to be snowy. cycleGAN, one of the prevailing condtional GAN, proposed by Zhu et al. is a good architecture for such tasks. In order to make the model more sensitive at inferencing which parts of the image should be changed most from one domain to another, we attempt to add an attention mechanism on cycleGAN. Mejjati et al. propose incorporating an attention mechanism so that the attention network can accurately find the foreground that is within the domain of our interest. The main contribution of our paper includes: we test the features of different generator and discriminator architectures and conclude that generator with U-Net structure
and patch discriminator and aMean-Square-Error GAN Loss would generate high quality of images; we incorporate a attention mechanism with cycleGAN on specific task of adding snow scenery to images and make the modification reasonable. [Link](/assets/pdfs/ucsd_snowy.pdf)

|Input|Output|
|:-:|:-:|
|<img src="/assets/images/ucsd-in-snow/original.png">|<img src="/assets/images/ucsd-in-snow/output.png">|
