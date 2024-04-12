# Stylizing 3D Gaussian Splatting Rendering
Anurag Parcha<br>

This work is built on top of the "3D Gaussian Splatting for Real-Time Radiance Field Rendering" work by Bernhard Kerbl, Georgios Kopanas, Thomas Leimkühler and George Drettakis. The repository to their work is: https://github.com/An-u-rag/stylized-gaussian-splatting. You can also find their paper here: https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/3d_gaussian_splatting_high.pdf. And their project page [here](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/).


<section class="section" id="BibTeX">
  <div class="container is-max-desktop content">
    <h2 class="title">BibTeX</h2>
    <pre><code>@Article{kerbl3Dgaussians,
      author       = {Kerbl, Bernhard and Kopanas, Georgios and Leimk{\"u}hler, Thomas and Drettakis, George},
      title        = {3D Gaussian Splatting for Real-Time Radiance Field Rendering},
      journal      = {ACM Transactions on Graphics},
      number       = {4},
      volume       = {42},
      month        = {July},
      year         = {2023},
      url          = {https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/}
}</code></pre>
  </div>
</section>

# Requirements
Clone the repository using: 
```
git clone https://github.com/An-u-rag/stylized-gaussian-splatting.git
```
All the requirements are the same as the one for 3DGS with no urther additions. Requirements can be seen [here]{https://github.com/graphdeco-inria/gaussian-splatting} or [this]{https://www.youtube.com/watch?v=UXtuigy_wYc} video tutorial can be followed for ease.

An environment.yml file is also provided to setup conda environment if needed. Create a new conda environment using belwo command:
```
SET DISTUTILS_USE_SDK=1 # Windows only
conda create --name stylegs --file environment.yml
conda activate stylegs
```

# To stylize pre-reconstructed gaussian splats
1. First download the datasets of multi view images from [here]{https://drive.google.com/file/d/1PDmtVKgfoLoJpNTrvFBh6NLah3j-04oL/view?usp=sharing}, unzip it and place the data folder into the root of this repo.
2. There are some pre reconstructed gaussian splats of scenes in this [link]{https://drive.google.com/file/d/1mpkHldrWK_PYh8iA8vs8GJyJzQWwXbkp/view?usp=sharing}. Download this folder into the root of this repository similar to step 1 to be used for training stylization.
3. Download the style images for stylization [here]{https://drive.google.com/file/d/1b118DXuFgPAihHrwnknvM09ev2aKga2P/view?usp=sharing} and place this folder into the root repository.
3. Then run the below code from the root of this repository to stylize the reconstructed gaussian splat. 
```
python train.py -s data/ferns --checkpoint_iterations 31000 32000 33000 --save_iterations 31000 32000 33000 --style_path style/s5.png --stylize_iter_start 30001 --data_device cpu --iterations 33000 --start_checkpoint preReconScenes/ferns_hq/chkpnt30000.pth --model_path output/ferns_s5
```
Note: -s argument is used to provide a path to multi view images. --style_path is used to provide the path to the input style image. --start_checkpoint is used to provide a path to the content reconstructed gaussian splat. --model_path is the output path of the trained model. The "stylize_iter_start" argument is 30001 which means that the checkpoint that we are loading in is the 30000 checkpoint and the next iteration onwards content params are frozen and only the style params are optimized. NOTE: For the bicycle dataset, the provided reconstructed gaussian splat is only of 25000 iteration, hence "stylize_iter_start" will have to be 25001 for bicycle stylization.

4. The stylized gaussian splat will appear in "output/ferns_s5" path. To view this, download the renderers by 3DGS team. [Viewers for Windows (60MB)](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/binaries/viewers.zip)
5. Follow instructiosn on 3DGS page for clearer instructions on this. 
6. Once downloaded and extracted to the root of project peository, navigate and run the below code:
```
cd viewers/bin
SIBR_gaussianViewer_app.exe -m path-to-root-repo/output/ferns_s5
```
Note: replace "path-to-root-repo" with the absolute path to where this repo is located on your pc.
7. Real time viewer will open up showing the stylized gaussian splat.

# To create your own dataset for 3DGS
For more detailed steps, follow 3DGS repository page to create your own dataset to run 3DGS. Here is the basic outline of the steps:

1. Create a folder and name it according to the dataset content.
2. Create a inner folder called "input" and place your multi-view images inside it.
3. Open your terminal / anaconda prompt and navigate to this cloned repository.
4. Run the below code:
```
python convert.py -s path/to/your/dataset/folder
```
Note: Dataset folder is the main folder and not the "input" folder.
5. You have your dataset created if the output in terminal says "Done". Else try again with a better dataset for feature correspondence.
6. Create your reconstructed gaussian splat of this scene using 3DGS by calling the below code:
```
python train.py -s path/to/your/dataset --densification_interval 1200 --densify_until_iter 7000 --checkpoint_iterations 7000 12000 25000 30000 --save_iterations 7000 12000 25000 30000 --data_device cpu --iterations 30000 --model_path output/dataset_name

```
7. You will have a new folder in your output folder with your "dataset_name". This is your reconstructed gaussian splat after 30000 iterations. Yo ucan use this as a path of "start_checkpoint" argument in the above sections for stylization.


