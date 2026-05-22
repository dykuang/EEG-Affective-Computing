# Preprocessing and Segmentation

This chapter focuses on how raw EEG is transformed into model-ready inputs for affective computing. The steps involved include artifact handling, filtering, normalization, epoching, and segmentation, but the deeper issue is methodological: each preprocessing choice defines assumptions about signal quality, time scale, and what information is allowed to enter the prediction pipeline.

Among these choices, segmentation is particularly important because it links the continuous EEG stream to the unit of inference used by the model. Window size, overlap, alignment, and label assignment all affect both learning behavior and evaluation validity.

The chapter therefore emphasizes preprocessing decisions that interact directly with downstream task design, especially in the presence of temporal dependence, overlapping windows, and online versus offline deployment assumptions.
