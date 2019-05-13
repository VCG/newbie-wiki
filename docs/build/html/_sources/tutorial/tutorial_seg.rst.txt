Segmentation
==========================

Neuron: SNEMI3D
---------------------

#. Computation

    #. Image -> Affinity

        #. Install `pytorch_connectomics <https://zudi-lin.github.io/pytorch_connectomics/build/html/tutorials/snemi.html>`_

    #. Affinity -> 3D Segmentation
        
        #. Install `waterz <https://github.com/donglaiw/waterz>`_
        
        #. Download affinity result 

            .. code-block:: none
            
                wget http://hp03.mindhackers.org/rhoana_product/snemi/aff/model_snemi_dice_mls._train_min.h5

        #. Usage

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

Synapse: CREMI
---------------------

Mitochondria: Lucchi
---------------------
