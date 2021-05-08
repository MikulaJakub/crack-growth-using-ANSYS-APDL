## Background
### Theory
High-cycle fatigue can be generally divided into three stages: crack **nucleation**, crack **growth**, and eventually brittle **failure**. Here, I focus on the second stage - stable crack growth. Under certain conditions, the crack growth can be described by the Paris-Erdogan law (logarithmically linear dependence between the number of cycles and the stress intensity factor K). Predictions of the lifetime (number of cycles) of a sample with a pre-existing crack or predictions of the crack path are of crucial importance in assessing the conditions of engineering structures subjected to cyclic loading, such as shafts, switches and others. Furthermore, the ability to predict the crack path using computer simulations allows one to optimize the structural properties with an effort to prevent the failure.

### Aims
The aim is to write an APDL script which can be used to model the crack growth in an arbitrary 2D geometry in ANSYS Mecanical APDL using the plane elements. The employed theory is based on the assumptions of the linear elastic fracture mechanics (LEFM).

### Algorithm
In this approach, the crack surfaces are modelled explicitly at each time step (crack increment) and the geometry (crack propagation zone) is (re-)meshed at each time step too. Although the remeshing approach may not (in terms of computational time) be very efficient when modelling a crack growth, it does the job.

The crack angle at each iteration is predicted from the stress intensity factors <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;K_{I}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;K_{I}" title="K_{I}" /></a> and <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;K_{II}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;K_{II}" title="K_{II}" /></a> calculated from the displacement field near the crack tip <a href="https://doi.org/10.1115/1.3656897">[Erdogan et al., 1963]</a>, (employing the special element property that models the crack singularity by shifting the element nodes to one-quarter of the element length):

<a href="https://www.codecogs.com/eqnedit.php?latex=\theta&space;=&space;2\arctan&space;\left(\frac{1}{4}\frac{K_I}{K_{II}}&space;\pm&space;\frac{1}{4}\sqrt{\left(\frac{K_I}{K_{II}}\right)^2&plus;8}\right)" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\theta&space;=&space;2\arctan&space;\left(\frac{1}{4}\frac{K_I}{K_{II}}&space;\pm&space;\frac{1}{4}\sqrt{\left(\frac{K_I}{K_{II}}\right)^2&plus;8}\right)" title="\theta = 2\arctan \left(\frac{1}{4}\frac{K_I}{K_{II}} \pm \frac{1}{4}\sqrt{\left(\frac{K_I}{K_{II}}\right)^2+8}\right)" /></a>

where the stress-intensity factors are calculated from the crack tip nodal displacements (see ANSYS's documentation)

<a href="https://www.codecogs.com/eqnedit.php?latex=K_I&space;=&space;\frac{E}{3(1&plus;\mu)(1&plus;\kappa)}\sqrt{\frac{2\pi}{L}}&space;\left[4(v_2-v_4)-\frac{v_3-v_5}{2}\right]\\&space;K_{II}&space;=&space;\frac{E}{3(1&plus;\mu)(1&plus;\kappa)}\sqrt{\frac{2\pi}{L}}\left[4(u_2-u_4)-\frac{u_3-u_5}{2}\right]" target="_blank"><img src="https://latex.codecogs.com/svg.latex?K_I&space;=&space;\frac{E}{3(1&plus;\mu)(1&plus;\kappa)}\sqrt{\frac{2\pi}{L}}&space;\left[4(v_2-v_4)-\frac{v_3-v_5}{2}\right]\\&space;K_{II}&space;=&space;\frac{E}{3(1&plus;\mu)(1&plus;\kappa)}\sqrt{\frac{2\pi}{L}}\left[4(u_2-u_4)-\frac{u_3-u_5}{2}\right]" title="K_I = \frac{E}{3(1+\mu)(1+\kappa)}\sqrt{\frac{2\pi}{L}} \left[4(v_2-v_4)-\frac{v_3-v_5}{2}\right]\\ K_{II} = \frac{E}{3(1+\mu)(1+\kappa)}\sqrt{\frac{2\pi}{L}}\left[4(u_2-u_4)-\frac{u_3-u_5}{2}\right]" /></a>

where <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\kappa=3-4\mu" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;\kappa=3-4\mu" title="\kappa=3-4\mu" /></a> for plane stress and <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\kappa=(3-\mu)/(1&plus;\mu)" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;\kappa=(3-\mu)/(1&plus;\mu)" title="\kappa=(3-\mu)/(1+\mu)" /></a> for plane strain.

## Demonstration
Here, I demonstrate how to prepare the geometry and run the code to predict the crack path. This demo was tested with Mechanical APDL 2019 R2 (Academic version - limited to 32000 nodes).


### Geometry



First, create the desired geometry model in 2D. As an example I choose the following 2D modified CT sample illustrated in the YouTube video.

Notice that the geometry has to contain an area (in this case A3 shown in the figure below) in which the crack is expected to propagate. This area will be (re)-meshed automatically. The keypoint of the crack initiation (KP = 27) and the expected final crack position (KPe = 33) have to be defined on the boundary (NOT within). The size of the crack propagation zone (A3) and the choice of the keypoint of the expected final crack position do not influence the result; however, they influence the efficiency (time to remesh) and stability of the solution.
