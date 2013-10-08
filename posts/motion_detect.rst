.. title: Motion Detection in 50 Lines of Python
.. slug: motion-detection-python
.. date: 2013/10/08 00:53
.. tags: image processing, modeling
.. link: 
.. description: The article describes a simple python implementation of a motion detection algorithm

A Poor Man's Motion Detector in 50 Lines of Python
==================================================

While experimenting with motion detection, I have implemented a very simple algorithm in python. The input is a stream of images (captured from a fixed webcam), and the motion detection approach consists of fitting a normal distribution to the past values of each pixel of the image. Like this, the probability of the value of a new pixel can be computed, and if it is very small then it is likely the pixel has changed. The animated GIF images below demonstrate how the algorithm works. 

.. raw:: html

	<table style="margin-left: auto; margin-right:auto" cellpadding="4" cellspacing="0">
		<tr>
			<td>
				<img style="height: 200px" src="/img/mdetect/pax_sit.gif" />
			</td>
			<td>
				<img style="height: 200px" src="/img/mdetect/pax_sit_blur.gif" />
			</td>
		</tr>
		<tr>
			<td>
				<img style="height: 200px" src="/img/mdetect/pax_sit_chmask.gif" />
			</td>
			<td>
				<img style="height: 200px" src="/img/mdetect/pax_sit_pchg.gif" />
			</td>
		</tr>
	</table>

.. TEASER_END: Click to read the entire article

In the upper left picture the frames are shown as they are captured from the webcam. The upper right image shows the frame as the computer sees it after some preprocessing (converting to greyscale and applying a gaussian blur for noise removal). Lower right we see the probability that each pixel has changed; black is 0 (i.e no change) and white is 1 (i.e. certain change). The lighter the shade of grey, the more confident the algorithm is that a change in the intensity values of the pixel has occured. We can observe that in the area where the dog's head is, the probability of change is higher because the dog moves his head and ears slightly, however this movement is not enough to trigger the motion detector. Applying a treshold to the change probability (in this example 0.98) we obtain a change mask shown in the lower left picture. The change mask shows which area of the image has changed. If the change area is bigger than some value (let's say 5% of the entire image) then the motionn detctor is triggered. 

The model assumes a stream of pixels:

.. figure :: /img/mdetect/eq_stream.png
	:height: 28px
	:align: center

The mean and standard deviation are computed as follows:

.. figure :: /img/mdetect/eq_ema.png
	:height: 32px
	:align: center

.. figure :: /img/mdetect/eq_emstd.png
	:height: 48px
	:align: center

This method is called the exponential moving average, and instead of taking all past values into account or working with a moving window, it only looks at the previous frame. The parameter alpha determines how fast the past values are "forgotten". If the user does not wish to take more than N previous frames into account, then he can set alpha to:

.. figure :: /img/mdetect/eq_alpha.png
	:height: 64px
	:align: center

The code implementing the above algorithm is `available on github`_.The slides which I have presented at the `Ljubljana Python Meetup`_ can be viewed `here`_.


.. Links

.. _available on github: https://github.com/lorserker/pmdetect
.. _Ljubljana Python Meetup: http://www.meetup.com/Ljubljana-Python-Group/events/141852972/
.. _here: /slides/motion_detect/index.html
