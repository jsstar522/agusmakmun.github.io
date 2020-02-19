---
layout: post
title:  "ACGAN(Auxiliary Classifier GAN)에 대해서"
date:   2020-02-19 05:07:00 +0900
categories: [gan]
use_math: true
---

`ACGAN`은 `CGAN`의 응용버전이다. 목적은 `CGAN`처럼, `latent vector`를 제어해서 원하는 fake image를 뽑아내기 위함이다. 학습하는 방식은 약간 다르다.

## 어떻게 작동되는가?

**CGAN과 다른점은 discriminator가 image class를 input으로 받지 않는다는 점이다.** 또한 discriminator가 real/fake를 output으로 내뱉었지만 ACGAN에서는 **class까지 판별**한다. 즉, output이 2개이다. 그래서 loss function이 2개다!

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200219/1.jpeg" alt="distribution" style="width:700px; margin: 0 auto;"/>

맨 오른쪽에 있는 모델이 ACGAN이다. 





## 전체코드

```python
#!/usr/bin/python
from tensorflow.keras.datasets import mnist
from tensorflow.keras.models import Sequential, Model
from tensorflow.keras.layers import Input, Dense, Activation, Flatten, Reshape, Embedding
from tensorflow.keras.layers import Conv2D, Conv2DTranspose, UpSampling2D
from tensorflow.keras.layers import LeakyReLU, Dropout, multiply
from tensorflow.keras.layers import BatchNormalization
from tensorflow.keras.optimizers import Adam, SGD, RMSprop

from tensorflow.keras.utils import plot_model

import keras.backend as K

import ROOT
ROOT.gROOT.SetBatch(True)
import os
import numpy as np
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
from matplotlib.colors import LogNorm

class ACGAN():
  def __init__(self):
	self.latent_dim = 100
	self.num_classes = 10
	self.height = 28
	self.width = 28
	self.channel = 1

	# discriminator Nets
	self.discriminatorModel = self.create_discriminator()
	self.discriminatorModel.compile(loss=['binary_crossentropy', 'sparse_categorical_crossentropy'], optimizer=Adam(0.0002, 0.5), metrics=['accuracy'])

	# gan Nets
	self.generatorModel = self.create_generator()
	self.generatorModel.compile(optimizer=Adam(0.0002, 0.5), loss='binary_crossentropy')

	noise = Input(shape=(self.latent_dim, ))
	image_class = Input(shape=(1, ))
	img = self.generatorModel([noise, image_class])
	for layer in self.discriminatorModel.layers:
	  layer.trainable = False
	fake, aux = self.discriminatorModel(img)
	
	self.ganModel = Model([noise, image_class], [fake, aux])
	self.ganModel.compile(loss=['binary_crossentropy', 'sparse_categorical_crossentropy'], optimizer=Adam(0.0002, 0.5), metrics=['accuracy'])

  def load_data(self):
	myFile = ROOT.TFile('/home/jsstar522/Project/CMSCaloGAN/DeepShowerSim_CMSSW_10_6_1/src/DeepShowerSim/0-Generation/rootData/24x24.root', 'read')
	myTree = myFile.Get('crystal')
	x_train = []
	for entry in myTree:
	  x_train.append(np.array(entry.energy_deposit))
	x_train = np.array(x_train)/50.0
	x_train = x_train.reshape(-1, 24, 24, 1)

	return x_train

  def create_generator(self):
	G = Sequential()

	G.add(Dense(1024, input_dim=self.latent_dim))
	G.add(LeakyReLU(alpha=0.2))
	G.add(Dense(128*7*7))
	G.add(LeakyReLU(alpha=0.2))
	G.add(Reshape((7, 7, 128)))

	G.add(UpSampling2D())
	G.add(Conv2DTranspose(256, 3, padding='same'))
	G.add(LeakyReLU(alpha=0.2))

	G.add(UpSampling2D())
	G.add(Conv2DTranspose(128, 3, padding='same'))
	G.add(LeakyReLU(alpha=0.2))

	G.add(Conv2DTranspose(1, 3, padding='same', activation='tanh'))

	G.summary()

	latent = Input(shape=(self.latent_dim,))
	## label for acgan
	image_class = Input(shape=(1, ), dtype='int32')
	label_embedding = Flatten()(Embedding(self.num_classes, self.latent_dim)(image_class))

	model_input = multiply([latent, label_embedding])
	output = G(model_input)

	plot_model(Model([latent, image_class], output), to_file='generator_plot.png', show_shapes=True, show_layer_names=True, expand_nested=False)	
	return Model([latent, image_class], output)

  def create_discriminator(self):
	D = Sequential()
	
	D.add(Conv2D(32, 3, strides=2, padding='same', input_shape=(28, 28, 1)))
	D.add(LeakyReLU(alpha=0.2))
	D.add(Dropout(0.3))

	D.add(Conv2D(64, 3, strides=2, padding='same'))
	D.add(LeakyReLU(alpha=0.2))
	D.add(Dropout(0.3))
	
	D.add(Conv2D(128, 3, strides=2, padding='same'))
	D.add(LeakyReLU(alpha=0.2))
	D.add(Dropout(0.3))
	
	D.add(Conv2D(256, 3, strides=2, padding='same'))
	D.add(LeakyReLU(alpha=0.2))
	D.add(Dropout(0.3))
	
	D.add(Flatten())
	D.add(LeakyReLU())
	D.summary()

	img = Input(shape=(self.width, self.height, self.channel))
	features = D(img)

	fake = Dense(1, activation='sigmoid', name='generation')(features)
	aux = Dense(10, activation='softmax', name='auxiliary')(features)

	plot_model(Model(img, [fake, aux]), to_file='discriminator_plot.png', show_shapes=True, show_layer_names=True, expand_nested=False)	
	return Model(img, [fake, aux])

  def train(self, epochs, batch_size, sample_interval):
	#x_train =  self.load_data()
	(X_train, y_train), (_, _) = mnist.load_data()
	X_train = (X_train.astype(np.float32) - 127.5)/ 127.5
	X_train = np.expand_dims(X_train, axis=3)
	y_train = y_train.reshape(-1, 1)

	valid = np.ones((batch_size, 1))
	fake = np.zeros((batch_size, 1))

	for epoch in range(epochs):
	  idx = np.random.randint(0, X_train.shape[0], batch_size)
	  imgs, labels = X_train[idx], y_train[idx]

	  noise = np.random.normal(0, 1, (batch_size, 100))
#	  gen_img = self.generatorModel.predict([noise, labels])
	  sampled_labels = np.random.randint(0, 10, batch_size).reshape(-1, 1)
	  gen_img = self.generatorModel.predict([noise, sampled_labels])

	  # Training discriminator Nets
	  d_loss_real = self.discriminatorModel.train_on_batch(imgs, [valid, labels])
	  d_loss_fake = self.discriminatorModel.train_on_batch(gen_img, [fake, sampled_labels])
	  d_loss = 0.5 * np.add(d_loss_real, d_loss_fake)

	  # Training gan Nets
	  g_loss = self.ganModel.train_on_batch([np.concatenate((noise, noise)), np.concatenate((sampled_labels, sampled_labels))], [np.concatenate((valid, valid)), np.concatenate((sampled_labels, sampled_labels))])

	  g_loss = self.ganModel.train_on_batch([noise, sampled_labels], [valid, sampled_labels])

	  if epoch % sample_interval == 0:
		print("%d epochs => D loss: %f, G loss: %f" % (epoch, d_loss[0], g_loss[0]))

	  if epoch % sample_interval == 0:
		self.sample_images(epoch)
  def sample_images(self, epoch):
	r, c = 2, 5
	noise = np.random.normal(0, 1, (r*c, 100))
	sampled_labels = np.arange(0, 10).reshape(-1, 1)

	gen_imgs = self.generatorModel.predict([noise, sampled_labels])	
	gen_imgs = 0.5*gen_imgs + 0.5

	fig, axs = plt.subplots(r, c)
	cnt = 0
	for i in range(r):
	  for j in range(c):
		cnt = 3
		axs[i, j].imshow(gen_imgs[cnt,:,:,0], cmap='gray')
		axs[i, j].set_title("Digit: %d" % sampled_labels[cnt])
		axs[i, j].axis('off')
	fig.savefig("/home/jsstar522/Project/CMSCaloGAN/DeepShowerSim_CMSSW_10_6_1/src/DeepShowerSim/1-Training/CMSGAN/cmsCalo_images_WGAN/generated_calo%d" % epoch)
	
	for i in range(r):
	  for j in range(c):
		cnt = 5
		axs[i, j].imshow(gen_imgs[cnt,:,:,0], cmap='gray')
		axs[i, j].set_title("Digit: %d" % sampled_labels[cnt])
		axs[i, j].axis('off')
	fig.savefig("/home/jsstar522/Project/CMSCaloGAN/DeepShowerSim_CMSSW_10_6_1/src/DeepShowerSim/1-Training/CMSGAN/cmsCalo_images_WGAN/generated_calo%d_2" % epoch)
	
	plt.close()

if __name__ == '__main__':
  acgan = ACGAN()
  acgan.train(epochs=20000, batch_size=32, sample_interval=500)
```



