---
layout: post
title:  "PCI Passthrough on Illumos KVM"
date:   2016-03-27 9:12:58 -0500
categories: jekyll update hardware machine-learning
---

I'm a huge fan of [Joyent](https://www.joyent.com/), and the SmartDataCenter 
(now Triton) platform is one that I would love to use for machine learning and 
data science. The emphasis on observability and post-mortem debugging makes it 
a fantastic platform for production systems and by virtue of being totally 
open source (unlike, say, AWS) it is impossible to be truly locked into the 
platform. It is also usable for on-premise development environments, which can 
result in huge savings versus testing and developing in a public cloud.

I am envisioning a series of Docker containers dishing out inference requests to [TensorFlow Serving](https://tensorflow.github.io/serving/architecture_overview) models which were developed and trained in an on-premise Triton deployment. I began work on [Passer](https://github.com/hypoalex/passer) to facilitate this design, and while it would work fine on CPUs, accelerating training and inference with GPUs would be ideal for peak performance.

There are two issues with this.

1. Illumos does not currently have NVidia drivers
2. PCI passthrough in KVM doesn't work

If PCI passthrough worked, we could provision KVM Linux instances through 
Triton and just use the native Linux NVidia drivers. This would probably be 
less of an uphill battle than trying to convince NVidia to do anything.

So, what are the issues with PCI passthrough? I believe that all of the systems at [Joyent engineering](https://eng.joyent.com/manufacturing/bom.html) have VT-d support, so we're essentially facing driver issues. In particular...

1. IOMMU support
2. A pci_stub driver to blacklist the GPUs 

The [IOMMU interfaces](http://garrett.damore.org/2008/09/iommu-comes-to-solaris-x86.html) are present, but the AMD64 code will need a rewrite (according to [@rmustacc](https://twitter.com/rmustacc)). So this isn't an infeasible thing.

    <rmustacc> Like anything else at that layer, it's not particularly hard as it
               is detailed.
    <rmustacc> You need to be very careful not to overlook something.

So, I may get around to exploring this in more depth at some point. 

For now, I'm just making some notes for posterity.

