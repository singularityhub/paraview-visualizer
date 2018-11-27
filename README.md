# Paraview Visualizer

For instructions on using the full Paraview Web directly from a Singularity container,
see [this post](https://ask.cyberinfrastructure.org/t/how-do-i-run-paraview-or-openfoam-on-an-hpc-resource/644/2)
on AskCyberinfrastructre. Continue reading below for using the Paraview Visualizer
via a Docker container, or Singularity container instance.

## Singularity
If you don't have nvidia on your host, use the [Singularity](Singularity) recipe. If you have
nvidia, use the [Singularity.nv](Singularity.nv) recipe:

```bash
$ sudo singularity build paraview.simg Singularity     # no nvidia
$ sudo singularity build paraview.simg Singularity.nv  # nvidia
```

If you need it, make a data folder to bind to on the host:

```bash
$ mkdir -p /tmp/data
```
Start the container instance, here we are naming it "visualizer"

```bash
$ singularity instance.start --bind /tmp/data:/data paraview.simg visualizer
```

You should now see the paraview interface running on [127.0.0.1](http://127.0.0.1)

![paraview.png](paraview.png)

The mapping to `/data` is where local web applications will load files from.

Also note that you *must* stop local web servers, including any Docker applications
running on that port. I'm not privy to how paraview works, but given this setup
you should be able to figure it out from here. Here is how to shell into the
container:

Huge thanks to [@jourdain](https://github.com/jourdain) for his detailed help and instruction to figuring this out! :D

## Interactive shell
I had needed to debug further to see how to get paraview working. Here is how to shell inside.


```bash
$ sudo singularity shell instance://paraview
```

## Cleaning Up

And to stop the container, you also need sudo

```bash
$ sudo singularity instance.stop instance://paraview
```

I'm not sure if this is reasonable to run in user space because of needing write
to /var/lock. Using sudo with singularity seems to defeat the purpose. If you
figure out a good approach please send a pull request to this repository!
Remember that to use nvidia, you would need to change the `From` line in
the Singularity file to `kitware/paraviewweb:pvw-egl-demo-v5.6.0` and then add
`--nv` to take advantage of the libraries on the host.

Also note that if you are using Singularity 3.0 and up the instance group is now changed 
to "instance stop" and "instance start"
