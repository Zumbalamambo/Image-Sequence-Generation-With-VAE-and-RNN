# Image sequence generation with VAE and RNN

This is just a quick :zap: experiment what I wanted to do.

The basic idea is that I encode frames with VAE from `(64x64x3)` to `(32,)` dim vectors and then use
RNN (GRU/LSTM) to generate new sequences.

## Train & Generate

You can find the details of the training and generation in this [Notebook](video_generation.ipynb)

## VAE encoded and then decoded frames

With the images I train the VAE and then I encode the frames to `32 dim` vectors.
After this I immediately decode the samples, and this is the result I get:

![img](art/vae_decoded_vs_original.gif)

## Generated sequence

With the previous version which was built without a Mixture Density Layer, the generated sequence was pretty static, it looked like an average
image. Then I read the first few sentences of the paper [Mixture Density Networks (MDN)](https://publications.aston.ac.uk/373/1/NCRG_94_004.pdf) and realized the problem.

> Minimization of a sum-of-squares or cross-entropy error function leads to network outputs
which approximate the conditional averages of the target data, conditioned on the
input vector. For classifications problems, with a suitably chosen target coding scheme,
these averages represent the posterior probabilities of class membership, and so can be
regarded as optimal. For problems involving the prediction of continuous variables, however,
the conditional averages provide only a very limited description of the properties
of the target variables.

1. Train VAE to encode images
2. Encode images to `32 dim` vectors
3. Create data batches (time steps) for the RNN with a sliding window
    - Shape of `X`: `(n_data, n_time_steps, 32)`, (32 is the output dim from the VAE)
    - Shape of `y`: `(n_data, 32)`
    - Intuitively: For `n_time_steps` consecutive frames in `X`, we can find the next frame `n_time_steps + 1` in the corresponding `y`.
4. Train the RNN (currently it is GRU with MDN)
5. Generate encoded images with RNN and decode it with the VAE

#### Previous version (without MD layer):

![img](art/generated_image_sequence_prev.gif)

#### Current version (with MD layer):

![img](art/generated_image_sequence.gif)
