### region-based active learning

- Prepare the initial training set, pool set, validation set and test data

  - | Filename                | Functions                                                    |
    | ----------------------- | ------------------------------------------------------------ |
    | Pre_Act_Region_Specific | The selection of the initial training data and validation data and prepare the pool data |

- Architecture for foreground-background segmentation and contour prediction	

  - | Filename                   | Functions                                                    |
    | -------------------------- | ------------------------------------------------------------ |
    | inference                  | The design of the main architecture ResNet_V2_DMNN for predicting the cells and the contour of the cells. It uses the ***resnet_v2*** for buiding encoder and ***layer_utils*** for building the decoder |
    | resnet_v2 and resnet_utils | ***resnet_v2*** designs the resnet block and it uses the functions in the ***resnet_utils*** to achieve it |
    | layer_utils                | The layers that are used in the ***inference*** script for building the decoder |
    | Loss_region_specific       | The loss functions that are used for training the ResNet_V2_DMNN model. |

- Selecting the most uncertain patches in each step

  - | Filename                  | Functions                                                    |
    | ------------------------- | ------------------------------------------------------------ |
    | dynamic_patch_search      | major functions for the region-based active learning (uncertainty metrics, selection criterias, and the method for determining the pseudo label for the foreground and contour of the cells) |
    | data_dealer_region_active | The function that is used to update the training data (pseudo label). It is possible that the images that are already selected before are selected again given the updated model, which means there will be multiple pseudo label for the same image. This function can aggregate all the pseudo labels for the same image. |

- Training script

  - | Filename                         | Functions                                                    |
    | -------------------------------- | ------------------------------------------------------------ |
    | Selec_Pool_Active_Region_Dynamic | evaluate the uncertainty of all the regions in each images in the pool set using the updated model. Then select the most uncertain patches using the function ***dynamic_patch_search*** |
    | Train_Active_Region_Dynamic      | The training script for region based active learning. <br />The order is:<br />1. Give the initial training data, pool data and validation data using function ***Pre_Act_Region_Specific*** <br />2. Train a ResNet model (***inference.py***) for identifying the cells and edges give the training data<br />3. Given this trained model and pool dataset, the model evalutes all the images in the pool set using file ***Selec_Pool_Active_Region_Dynamic***. Then it selects the most uncertain regions within the pool set using ***dynamic_patch_search***. These most uncertain regions together with their pseduo label are send back to the training set .<br />4. (optional) If the model already selected some regions from the pool set, then we need to use the function ***data_dealer_region_active*** to give the overall updated training data<br />5. Then we retrain a new model given the updated training set<br />6. We repeat step 2 to 5 until the performance on the test data stays stable |
    | Test_FB_ResNet_Active            | The test script. It saves the foreground-background probability, and the contour probability in a mat version. Then these saved data are used in the matlab file to give the final performance |
    |                                  |                                                              |

- Post Processing for analyzing the results

  - Analyze (give the catastrophic plot)
  - Visualize
  - Visualize_Region
  - collect_Fg_diff_act_step_region_dynamic: collect the selected regions for all the acquisition steps
