---
id: 459
title: 'Duhamel&#8217;s Integral'
date: '2016-09-03T21:50:19+02:00'
author: David
layout: revision
guid: 'https://david.gyttja.com/2016/09/03/298-revision-v1/'
permalink: '/?p=459'
---

### Testing markdown and latex

Duhamel's convolution integral can be used to approximate the response of SDOF structures to a general dynamic loading.

[mathjax]
$$x(t) = \frac{1}{m\omega_{D}} \int_0^t p(\tau) \, e^{-\xi\omega(t - \tau)} \, \sin \omega_{D} (t - \tau) \, \mathrm{d}\tau$$

$$\dot{x}(t) = \frac{1}{m\omega_{D}} \int_0^t p(\tau) \, e^{-\xi \omega (t - \tau)} \, \left( \omega_{D} \cos \omega_{D}(t - \tau) \, - \, \xi \omega \sin \omega_{D}(t - \tau) \right) \, \mathrm{d}\tau$$

$$\ddot{x}(t) = \frac{1}{m\omega_{D}} \left( \omega_{D} \, p(\tau) \, + \int_0^t p(\tau) \, e^{-\xi \omega (t - \tau)} \, \left( \xi \omega^2 \sin \omega_{D}(t - \tau) \, - \, 2 \xi \omega \omega_{D} \cos \omega_{D}(t - \tau) \, - \, \omega_{D}^2 \sin \omega_{D}(t - \tau) \right) \mathrm{d}\tau \right)$$

Align by equals:

[mathjax]
$$\begin{aligned}
x(t) &= \frac{1}{m\omega_{D}} \int_0^t p(\tau) \, e^{-\xi\omega(t - \tau)} \, \sin \omega_{D} (t - \tau) \, \mathrm{d}\tau \\\\
\dot{x}(t) &= \frac{1}{m\omega_{D}} \int_0^t p(\tau) \, e^{-\xi \omega (t - \tau)} \, \left( \omega_{D} \cos \omega_{D}(t - \tau) \, - \, \xi \omega \sin \omega_{D}(t - \tau) \right) \, \mathrm{d}\tau \\\\
\ddot{x}(t) &= \frac{1}{m\omega_{D}} \left( \omega_{D} \, p(\tau) \, + \int_0^t p(\tau) \, e^{-\xi \omega (t - \tau)} \, \left( \xi \omega^2 \sin \omega_{D}(t - \tau) \, - \, 2 \xi \omega \omega_{D} \cos \omega_{D}(t - \tau) \, - \, \omega_{D}^2 \sin \omega_{D}(t - \tau) \right) \mathrm{d}\tau \right)
\end{aligned}$$

Now testing some other text here. I want to see how code markup looks, so let's see how it is rendered.

    $ ls -l
    $ rm -rf /

```css
.ninja {
  visibility: hidden;
}
```

So that's that.