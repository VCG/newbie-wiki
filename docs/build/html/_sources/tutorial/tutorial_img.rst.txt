Segmentation
=============

SNEMI3D
------------------

1. Download Data

    .. code-block:: none

        wget http://140.247.107.75/rhoana_product/snemi/image/train-input.tif
        wget http://140.247.107.75/rhoana_product/snemi/seg/train-labels.tif
        wget http://140.247.107.75/rhoana_product/snemi/image/test-input.tif

2. Computation
    - Image Deflicker
        - Install `EM-preprocess <https://github.com/donglaiw/EM-preprocess>`_
        - Usage

            .. code-block:: python
           
                from em_pre.deflicker import deflicker_online
                import tifffile

                ims = tifffile.imread('train-input.tif')
                def getN(i):
                    return ims[i]
                result = deflicker_online(getN, opts=[0,0,0], globalStat=[150,-1], filterS_hsz=[15,15], filterT_hsz=2)

    - Affinity Training and Inference
        - TBD
    - 3D Segmentation
        - Install `waterz <https://github.com/donglaiw/waterz>`_
        - Download affinity result 

            .. code-block:: none
            
                wget http://140.247.107.75/rhoana_product/snemi/aff/model_snemi_dice_mls._train_min.h5
        - Usage

            .. code-block:: python

                import waterz
                import h5py
                import numpy as np

                aff_thresholds = [0.005, 0.995]
                seg_thresholds = [0.1, 0.3, 0.6]
                aff = np.array(h5py.File('model_snemi_dice_mls._train_min.h5','r')['main'])
                result = waterz.waterz(aff, seg_thresholds,
                                merge_function='aff85_his256',
                                aff_threshold=aff_thresholds)

3. Visualization
    - Install python back-end
        
        .. code-block:: none 
      
            pip install neuroglancer

    - Install NodeJS front-end: `README.md <https://github.com/google/neuroglancer#building>`_
    - Usage: run on ipython/jupyter notebook or `python -i THIS_FILE.py`

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
            res=[6,6,30]; # resolution of the data
            print 'load im'
            im = tifffile.imread('train-input.tif')
            with viewer.txn() as s:
                s.layers.append(
                    name='im',
                    layer=neuroglancer.LocalVolume(
                        data=im,
                        voxel_size=res
                    ))

            print 'load gt'
            gt = tifffile.imread('train-labels.tif')
            with viewer.txn() as s:
                s.layers.append(
                    name='gt',
                    layer=neuroglancer.LocalVolume(
                        data=gt.astype(np.uint16),
                        voxel_size=res
                    ))

            print viewer
