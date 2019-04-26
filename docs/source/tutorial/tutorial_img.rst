Image Preprocess
=================

Image Deflicker
------------------
#. Install `EM-preprocess <https://github.com/donglaiw/EM-preprocess>`_
#. Usage

    .. code-block:: python
   
        from em_pre.deflicker import deflicker_online
        import tifffile

        ims = tifffile.imread('train-input.tif')
        def getN(i):
            return ims[i]
        result = deflicker_online(getN, opts=[0,0,0], globalStat=[150,-1], filterS_hsz=[15,15], filterT_hsz=2)
