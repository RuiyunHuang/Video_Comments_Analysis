Results can be found [here](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/7965635341886967/4455522408109164/5164190521743747/latest.html)

# Business Case 

We are a social media company providing advertisement services on our video website. Recently, we received a contract from a pet food company that wants to put an advertisement about their new product and increase its selling profit across the country. 
 
We can help them achieve the goal from two major aspects:  Profit = Revenue – Cost.

The revenue is the unit price of their product times the total number of sales. We can increase the total number of sales by increasing the number of customers who buy this product. The buyers are mostly pets owners. So, on the one hand, we can put the advertisement in the video to users who have cats/dogs. On the other hand, we can put it on the page of those channels which are popular among pets owners. Those would potentially increase the number of customers. 

The cost is the amount of time and frequency of the advertisement was viewed on our website. By targeting pet owners, we can efficiently reduce unnecessary cost on less related users who are not pets owners. 


**The final question for us is to identify those users who have dogs or cats.**


To solve this question, I used data for animal videos from last year. The data includes channel creator names, audience id, and their comments. I identified cat or dog owners based on users’ comments and found video creators with a large number of audiences who are cat or dog owners. I also did an analysis of their common topics which may have business value in the future.

# Solution 

## Step 1: Identify cats/dogs owners

Since the raw data is the user comments, it is hard to directly use them for prediction. Here we used a method based on the skip-gram model. The skip-gram model is already implemented by the Word2Vec module in the Pyspark. It translated the words in comments into a representative vector by considering the correlation among nearby words as well as the whole corpus. The result is a vector that represents the comment.

Before the transformation, I screened and labeled cat/dog owners based on their comments.  For this labeling,  typical keywords I used are strong indications of the owner such as “my dog” and “my cat”. Then the assumption was that those labelings are ‘accurate’. By doing this, around 1% users are directly labeled as pet owners. Those part of data was used as positive case for model training. Then the comments are transformed by Word2Vec.

For model training, the original dataset was split by in a way to ensure both training and test datasets had a reasonable ratio of  1 and 0 to avoid data skewness.

## Step 2: Build classifiers for the cat/dog owners and measure the performance of the classifiers

Logistic regression, random forest and gradient boosted tree models were used and compared. Cross-validation was used to tuning hyperparameter for each model. The threshold of probability was set as 0.5. Then best model was chosen by comparing their performance on the test data. Random forest was chosen due to high AOC.


![figures](https://github.com/RuiyunHuang/Video_Comments_Analysis/blob/master/figures/evaluation.png)


## Step 3: Apply the classifiers to all the users then estimate the fraction of all users who are cat/dog owners

By applying the best model to all the users, we can predict who are pet owners. So in the future, we can include this advertisement for those users. 

I find that around 21.7 % of users (0.5 million) are pet owners. If 10% of those users buy their product (40 dollars per month), it is a market around 2 million per month.  This also means around 1 in 5 people who watch animal videos are dog/cat owners. So there is a bigger potential market by considering older data.


![figures](https://github.com/RuiyunHuang/Video_Comments_Analysis/blob/master/figures/users.png)


## Step 4: Find creators with the highest statistically significant percentages of the audience who are cat/dog owners

Good channels for this advertisement are listed. 0.05 was used as a statistically significant threshold. In the future, we can put the  advertisement on those channels.

![figures](https://github.com/RuiyunHuang/Video_Comments_Analysis/blob/master/figures/creators.png)

# Next Steps:

Before the implementation, it is helpful to do a A/B testing on a small scale to rule out possible risks.
 
