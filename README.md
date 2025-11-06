# AutomatedHigh-ThroughputQuantification

Multiplex immunohistochemistry (mIHC) technologies enable the simultaneous detection of multiple markers on a single tissue section, providing valuable quantitative data for understanding disease heterogeneity and molecular profiling. Various mIHC techniques have been developed, including chromogenic mIHC, TSA-based fluorescence mIHC (TSA-mIHC/IF), metal-based mIHC, cyclic mIHC/IF, and DNA barcoding-based mIHC, each with its advantages and limitations. Few studies have investigated tissue biomarkers in large cohorts of human tumors. This gap might be partially due to the lack of standardized platforms for high throughput in situ biomarker evaluation and the high associated costs. To address this issues, we established a versatile TSA-mIHC/IF platform that integrates standard histology equipment with the open-source software CellProfiler. This platform enables automated image processing for high-throughput spatial biomarker analysis with advantages of high sensitivity and specificity, cost-effectiveness, reproducibility, consistent staining, and enhanced multiplexing capacity through cyclic staining. We successfully applied this optimized workflow to quantitatively measure the expression of three inflammatory biomarkers related to polyunsaturated fatty acids in 791 extant human adenoma samples from the Tennessee Colorectal Polyp Study.  Through our Github platform, we provide detailed critical settings of the image processing pipelines used in the TSA-mIHC/IF workflow, ensuring users can achieve robust, consistent, and reproducible results across large sample sets. We anticipate that our work will make TSA-mIHC/IF more accessible to standard histology laboratories, facilitate large-scale biomarker studies in oncology and broader biomedical research, and contribute to ongoing efforts to standardize mIHC methodologies, particularly in assay optimization and image analysis. By providing a robust and automated solution for autofluorescence (AF) elimination and spatial biomarker quantification, our optimized TSA-mIHC/IF workflow enhances the precision and automation of mIHC analysis, ultimately advancing our understanding of disease mechanisms and exploring of biomarkers relevant for preventive interventions and targeted therapies for human neoplasms.

## Step 1: Overlay Three Biomarkers
Image acquisition to obtain original 16-bit grayscale TIFF images using the fluorescent microscope. Use ![GrayToColor](https://img.shields.io/badge/GrayToColor-red) modules to transfer invisible 16-bit grayscale images to visible RGB images.  This step generates overlaid color images for a visual quality check, allowing pathologists to assess image integrity and flag low-quality acquisitions. This quality control process is essential to ensure that only high-quality images are included in downstream analyses, thereby enhancing the reliability and reproducibility of biomarker quantification. Images flagged as low-quality may be quantified separately using an adjusted processing pipeline.
![Step1](https://github.com/user-attachments/assets/876eca65-b4e8-41d7-b03d-5bfb82a76c0b)

## Step 2: Identification of target proteins and AF
Place the six raw grayscale images (DAPI, AF, COX-2, PTGER4, PIK3CA, and Pan CK) into six separate image processing pipelines. 
![Figure-pip-1-2](https://github.com/user-attachments/assets/db327b43-e01b-4858-bced-2ffef8c4b4d1)

Use ![IdentifyPrimaryObjects](https://img.shields.io/badge/IdentifyPrimaryObjects-red) modules with advanced settings to identify objects of AF, cell nuclei, epithelial/tumor cells, COX-2, PTGER4, and PIK3CA. This is a key step of machine learning using selected 30 - 50 representative images from TMA controls and study samples, covering a range of staining intensities (negative, weak, moderate, and strong) to train image analysis pipelines. To validate the pipelines, we randomly selected 100 sample images to further adjust the pipelines until all quantitative scores aligned with pathologists’ visual scoring.
![Figure-pip-4](https://github.com/user-attachments/assets/7ecde452-05f9-4edd-aa44-439e55fab7fc)

Use ![FilterObjects](https://img.shields.io/badge/FilterObjects-red) modules to remove the small objects (noise) less than 40 pixels (in area value) for three biomarker images and objects less than 14 pixels (in area value) for DAPI and Pan CK images across all study samples.
![Figure-pip-5-6](https://github.com/user-attachments/assets/5ffecbef-d72e-4dc8-8ec4-1a90c0a49945)

Use ![SaveImages](https://img.shields.io/badge/SaveImages-red) module to save ObjectsIdentified images into a designated output folder.
![Figure-pip-10](https://github.com/user-attachments/assets/98e9ab75-ceff-45ec-97a2-0ac65472ff81)

## Step 3: Identify Total Tissue
PIK3CA images were loaded into a pipeline in which the ![IdentifyPrimaryObjects](https://img.shields.io/badge/IdentifyPrimaryObjects-red) module was set with a lower threshold value to identify total tissues. The resulting “IdentifiedTotalTissueObjects” images, along with six sets of identified object images, were saved into the designated output folder.
<img width="2093" height="1027" alt="TotalTissueIdentify" src="https://github.com/user-attachments/assets/8a789c4f-5ece-4f89-8eec-10e91ba1e819" />


## Step 4: Eliminate AF objects and automate quantification of three biomarkers
### Eliminate AF objects
Input the six sets of “ObjectsIdentified” images and one set of “IdentifiedTotalTissueObjects ” images into an image measurement pipeline for continued image processing.
![Figure-AF-remove](https://github.com/user-attachments/assets/df89189a-a569-4b0d-9cc9-ca25059ac3fc)

Use ![ConvertImageToObjects](https://img.shields.io/badge/ConvertImageToObjects-red) module to convert loaded visible images to calculable objects. 
![Figure-AF-remove-2](https://github.com/user-attachments/assets/597dfa60-6dcf-47ca-924b-137caa906baf)

Use ![ExpandObjects](https://img.shields.io/badge/ExpandObjects-red) module to expand AF objects by 3 pixels to fully remove tissue foldings. 
![Figure-AF-remove-3](https://github.com/user-attachments/assets/0971c435-bc2a-4932-87b3-85ab5247ea2c)

Use ![MaskObjects](https://img.shields.io/badge/MaskObjects-red) modules to remove AF objects, tissue folding, and edge effects by overlaying AF images with other fluorophore images to obtain five AF-free images for each sample. 
![Figure-AF-remove-4](https://github.com/user-attachments/assets/f6427e3a-c9f8-482a-a65b-4c13b8ff066e)

### Automate quantification of three biomarkers
Use ![RelateObjects](https://img.shields.io/badge/RelateObjects-red) modules to exclude objects not associated with cell nuclei in biomarker images, defining these objects as nonspecific fluorescent signals.
![Figure-AF-Qua-1](https://github.com/user-attachments/assets/66c230cc-1c84-4ae6-862c-8ce58b943d9f)

The ![IdentifySecondaryObjects](https://img.shields.io/badge/IdentifySecondaryObjects-red) module was used to fill the small cytoplasmic holes in the glandular structure of adenomas to define epithelial area more precisely. 
![Figure-AF-Qua-2](https://github.com/user-attachments/assets/5f85a2d7-fbf1-4495-b7c9-1a4d73385308)

Use ![MaskObjects](https://img.shields.io/badge/MaskObjects-red) modules to identify positive biomarker objects in tumor cells and stromal areas, respectively. It also obtained stromal objects by removing overlapping Pan CK-positive objects from the “IdentifiedTotalTissueObjects” images.
![Figure-AF-Qua-3](https://github.com/user-attachments/assets/efc93179-c7b6-42b5-861a-139037e2c7ff)

Use ![ConvertObjectsToImage](https://img.shields.io/badge/ConvertObjectsToImage-red) modules to convert identified objects into images for further measurement, and the “MeasureImageAreaOccupied” modules measure the areas occupied by identified objects.
![Figure-AF-Qua-4](https://github.com/user-attachments/assets/09288ac1-78f9-4208-b57f-66fa727a5598)

A set of ![CalculateMath](https://img.shields.io/badge/CalculateMath-red) modules automatically calculated the positive percentage of tumor cells, stromal cells, and total positive percentage for each biomarker.
