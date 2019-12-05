Visualization
==========================

Neuroglancer
---------------------
3D image stack and segmentation visualization
    
#. Install python back-end
    
    .. code-block:: none 

        pip install neuroglancer

#. Install NodeJS front-end: `README.md <https://github.com/google/neuroglancer#building>`_

#. Usage: 

   #. mount `/n/coxfs01/`  unto your local machine (see ``Setup/Computation``)

   #. run on ipython/jupyter notebook or `python -i THIS_FILE.py`

    .. code-block:: python
        
        import neuroglancer
        import numpy as np
        import sys
        import tifffile

        ip='localhost' # or public IP of the machine for sharable display
        port=98092 # change to an unused port number
        neuroglancer.set_server_bind_address(bind_address=ip,bind_port=port)
        viewer=neuroglancer.Viewer()

        # SNEMI
        D0='/mnt/coxfs01/vcg_connectomics/snemi/'
        res=[6,6,30]; # resolution of the data
        print('load im')
        im = tifffile.imread(D0+'img/train-input.tif')
        with viewer.txn() as s:
            s.layers.append(
                name='im',
                layer=neuroglancer.LocalVolume(
                    data=im,
                    voxel_size=res
                ))

        print('load gt')
        gt = tifffile.imread(D0+'label/train-labels.tif')
        with viewer.txn() as s:
            s.layers.append(
                name='gt',
                layer=neuroglancer.LocalVolume(
                    data=gt.astype(np.uint16),
                    voxel_size=res
                ))

        print(viewer)


