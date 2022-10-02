# ML project: predicting_animal_behavior
Predict animal motion onsets using neural activity data with various ML techniques.

## Goals
Determine if there is a relationship between the behavior events in triggers.csv and the neural data in the ca_data csv files. If there is a relationship, characterize it. Consider the 4 ca_data csv files as independent experiments (i.e. recordings from different animals).


## Analysis: is there a relationship between the neural data and behavior events (i.e., motion onset)?
If we are able to predict some aspect of the behavioral events with neural data at above chance level, then that would be evidence that there is a relationship between the neural data and behavioral events.

There are many ways to implement this analyses. I will approach this problem by using as little domain knowledge as possible (e.g. knowledge about calcium signal timescales, typical timescale of relationship between calcium signal and behavior, does neural signal for a behavio typically come before or after the behavior...etc). Given the lack of information about this dataset, this approach will allow me to make minimal numbers of assumption (i.e. minimize chances of incorrect assumptions).

**The specific question I will seek to answer is: does neural data in a selected time window predict the amount of motion onset in the same time window? (e.g. if I know the neural data for an animal from the time window 1000-2000, do I have some knowledge about the amount of behavior in the same time window?)**

To further simplify this question to make it tractable for a short project, we will:

1. Reduce the neural data from each time window to an array of mean CA activity for each neuron (e.g. if neural data for a window size of 1000 and 60 neurons will be reduced from 60x1000 matrix to 60x1 array by taking the average within each neuron).
2. Simplify this prediction problem to a binary classification problem by binarizing the amount of behavior that is being predicted for each time window into low or high activity through median splitting (If a time window contains 2 motion onset activity, then it will be assigned as a low acitivty time window if the median number of onset activity for the distribution of all windows is above 2).
3. 
Here is an example prediction process for a single time window: the prediction model will take as input a 1 dimensional array representing the average calcium acitivity for each neuron in that time window (e.g. 60x1 array for 60 neurons), and the model will output a binary prediction of whether the amount of acitivity in this time window is above or below the median for all time windows.

If this model is able to make predictions at above chance levels, then there is evidence for a relationship between the neural data and behavioral events.

The specific steps of this analyses is as follows (done separately for each animal):

1. Select a window size to bin the neural and behavioral data. This will be a bit arbitrary since there is not much information about the data (but we can still use the density of the behavioral events and visual inspection of the neural data time series to make some educated selections for window sizes). While the time window we choose may not be the most 'optimal,' we can still conclude a relationship between neural data and behavior as long as there is above chance classification for any time window. In this sense, the specific time window size does not matter too much.
2. Bin the data using a sliding-window method.
3. For the neural data, we compute the mean acitivity for each neuron in each time window.
4. For the behavioral data, we first compute the number of motion onset events in each time window. Then we compute the median of number of onsets across all timewindows. Each window is assigned as either high activity (if number of onset events is larger than median) or low activity (if number of onset events is equal or less than median).


A couple things to note:

1. Because the number of onset events is not continous, the median split will in general not result in a even 50-50 split of high and low activity assignments for the time windows. This unbalanced data means that chance level prediction accuracy for low/high activity will not be 50%. To address this issue, we will train models with unbalanced weights so that chance level performance is at 50%
2. Some of the steps taken here reduced the complexity of the data (e.g. binarize behavior into high/low and averaging calcium activity across time). This was done in part to make the problem more tractable. While this may increase the chance of false negative conclusions (e.g. failure to find a neural-behavior relationship from the analyses when in fact there is one), it does not affect our ability to intrepret an above chance prediction as evidence that there is a relationship between neural data and behavior.
