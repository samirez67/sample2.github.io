---
title: Semantic Analysis with R
author: "Giulia Grisot"
---

**Sentiment analysis helps you extract an author's feelings towards a subject. This exercise will give you a taste of what's to come!**

We created text_df representing a conversation with person and text columns.

Use qdap's polarity() function to score text_df. polarity() will accept a single character object or data frame with a grouping variable to calculate a positive or negative score.

In this example you will use the magrittr package's dollar pipe operator %$%. The dollar sign forwards the data frame into polarity() and you declare a text column name or the text column and a grouping variable without quotes.

text_data_frame %$% polarity(text_column_name)
To create an object with the dollar sign operator:

polarity_object <- text_data_frame %$% 
  polarity(text_column_name, grouping_column_name)
More specifically, to make a quantitative judgement about the sentiment of some text, you need to give it a score. A simple method is a positive or negative value related to a sentence, passage or a collection of documents called a corpus. Scoring with positive or negative values only is called "polarity." A useful function for extracting polarity scores is counts() applied to the polarity object. For a quick visual call plot() on the polarity() outcome.

Examine the text_df conversation data frame.
Using %$% pass text_df to polarity() along with the column name text without quotes. This will print the polarity for all text.
Create a new object datacamp_conversation by forwarding text_df with %$% to polarity(). Pass in text followed by the grouping person column. This will calculate polarity according to each individual person. Since it is all within parentheses the result will be printed too.
Apply counts() to datacamp_conversation to print the specific emotional words that were found.
plot() the datacamp_conversation.

```{r}
# Examine the text data
text_df

# Calc overall polarity score
text_df %$% polarity(text)

# Calc polarity score by person
(datacamp_conversation <- text_df %$% polarity(text, person))

# Counts table from datacamp_conversation
counts(datacamp_conversation)
```

## TM refresher (I)
In the Text Mining: Bag of Words course you learned that a corpus is a set of texts, and you studied some functions for preprocessing the text. To recap, one way to create & clean a corpus is with the functions below. Even though this is a different course, sentiment analysis is part of text mining so a refresher can be helpful.

Turn a character vector into a text source using VectorSource().
Turn a text source into a corpus using VCorpus().
Remove unwanted characters from the corpus using cleaning functions like removePunctuation() and stripWhitespace() from tm, and replace_abbreviation() from qdap.
In this exercise a custom clean_corpus() function has been created using standard preprocessing functions for easier application.

clean_corpus() accepts the output of VCorpus() and applies cleaning functions. For example:

processed_corpus <- clean_corpus(my_corpus)

**Instructions**

Your R session has a text vector, tm_define, containing two small documents and the function clean_corpus().

Create an object called tm_vector by applying VectorSource() to tm_define.
Make tm_corpus using VCorpus() on tm_vector.
Use content() to examine the contents of the first document in tm_corpus.
Documents in the corpus are accessed using list syntax, so use double square brackets, e.g. [[1]].
Clean the corpus text using the custom function clean_corpus() on tm_corpus. Call this new object tm_clean.
Examine the first document of the new tm_clean object again to see how the text changed after clean_corpus() was applied.


```{r}
# clean_corpus(), tm_define are pre-defined
clean_corpus
tm_define

# Create a VectorSource
tm_vector <- VectorSource(tm_define)

# Apply VCorpus
tm_corpus <- VCorpus(tm_vector)

# Examine the first document's contents
content(tm_corpus[[1]])

# Clean the text
tm_clean <- clean_corpus(tm_corpus)

# Reexamine the contents of the first doc
content(tm_clean[[1]])
```

## TM refresher (II)
Now let's create a Document Term Matrix (DTM). In a DTM:

Each row of the matrix represents a document.
Each column is a unique word token.
Values of the matrix correspond to an individual document's word usage.
The DTM is the basis for many bag of words analyses. Later in the course, you will also use the related Term Document Matrix (TDM). This is the transpose; that is, columns represent documents and rows represent unique word tokens.

You should construct a DTM after cleaning the corpus (using clean_corpus()). To do so, call DocumentTermMatrix() on the corpus object.

tm_dtm <- DocumentTermMatrix(tm_clean)
If you need a more in-depth refresher check out the Text Mining: Bag of Words course. Hopefully these two exercises have prepared you well enough to embark on your sentiment analysis journey!

Be aware that this is real data from Twitter and as such there is always a risk that it may contain profanity or other offensive content (in this exercise, and any following exercises that also use real Twitter data).

**Instructions**

We've created a VCorpus() object called clean_text containing 1000 tweets mentioning coffee. The tweets have been cleaned with the previously mentioned preprocessing steps and your goal is to create a DTM from it.

Apply DocumentTermMatrix() to the clean_text corpus to create a term frequency weighted DTM called tf_dtm .
Change the DocumentTermMatrix() object into a simple matrix with as.matrix(). Call the new object tf_dtm_m.
Check the dimensions of the matrix using dim().
Use square bracket indexing to see a subset of the matrix.
Select rows 16 to 20, and columns 2975 to 2985
Note the frequency value of the word "working."


```{r}
# clean_text is pre-defined
clean_text

# Create tf_dtm
tf_dtm <- DocumentTermMatrix(clean_text)

# Create tf_dtm_m
tf_dtm_m <- as.matrix(tf_dtm)

# Dimensions of DTM matrix
dim(tf_dtm_m)

# Subset part of tf_dtm_m for comparison
tf_dtm_m[16:20, 2975:2985]
```

## Where can you observe Zipf's law?
Although Zipf observed a steep and predictable decline in word usage you may not buy into Zipf's law. You may be thinking "I know plenty of words, and have a distinctive vocabulary". That may be the case, but the same can't be said for most people! To prove it, let's construct a visual from 3 million tweets mentioning "#sb". Keep in mind that the visual doesn't follow Zipf's law perfectly, the tweets all mentioned the same hashtag so it is a bit skewed. That said, the visual you will make follows a steep decline showing a small lexical diversity among the millions of tweets. So there is some science behind using lexicons for natural language analysis!

In this exercise, you will use the package metricsgraphics. Although the author suggests using the pipe %>% operator, you will construct the graphic step-by-step to learn about the various aspects of the plot. The main function of the package metricsgraphics is the mjs_plot() function which is the first step in creating a JavaScript plot. Once you have that, you can add other layers on top of the plot.

An example metricsgraphics workflow without using the %>% operator is below:

metro_plot <- mjs_plot(data, x = x_axis_name, y = y_axis_name, show_rollover_text = FALSE)
metro_plot <- mjs_line(metro_plot)
metro_plot <- mjs_add_line(metro_plot, line_one_values)
metro_plot <- mjs_add_legend(metro_plot, legend = c('names', 'more_names'))
metro_plot

**Instructions**

Use head() on sb_words to review top words.
Create a new column expectations by dividing the largest word frequency, freq[1], by the rank column.
Start sb_plot using mjs_plot().
Pass in sb_words with x = rank and y = freq.
Within mjs_plot() set show_rollover_text to FALSE.
Overwrite sb_plot using mjs_line() and pass in sb_plot.
Add to sb_plot with mjs_add_line().
Pass in the previous sb_plot object and the vector, expectations.
Place a legend on a new sb_plot object using mjs_add_legend().
Pass in the previous sb_plot object
The legend labels should consist of "Frequency" and "Expectation".
Call sb_plot to display the plot. Mouseover a point to simultaneously highlight a freq and Expectation point. The magic of JavaScript!


```{r}
# Examine sb_words
head(sb_words)

# Create expectations
sb_words$expectations <- sb_words %$% 
  {freq[1] / rank}

# Create metrics plot
sb_plot <- mjs_plot(sb_words, x = rank, y = freq, show_rollover_text = F)

# Add 1st line
sb_plot <- mjs_line(sb_plot)

# Add 2nd line
sb_plot <- mjs_add_line(sb_plot, expectations)

# Add legend
sb_plot <- mjs_add_legend(sb_plot, legend = c("Frequency", "Expectation"))

# Display plot
sb_plot
```

## Polarity on actual text
So far you have learned the basic components needed for assessing positive or negative intent in text. Remember the following points so you can feel confident in your results.

The subjectivity lexicon is a predefined list of words associated with emotions or positive/negative feelings.
You don't have to list every word in a subjectivity lexicon because Zipf's law describes human expression.
A quick way to get started is to use the polarity() function which has a built-in subjectivity lexicon.

The function scans the text to identify words in the lexicon. It then creates a cluster around an identified subjectivity word. Within the cluster valence shifters adjust the score. Valence shifters are words that amplify or negate the emotional intent of the subjectivity word. For example, "well known" is positive while "not well known" is negative. Here "not" is a negating term and reverses the emotional intent of "well known." In contrast, "very well known" employs an amplifier increasing the positive intent.

The polarity() function then calculates a score using subjectivity terms, valence shifters and the total number of words in the passage. This exercise demonstrates a simple polarity calculation. In the next video we look under the hood of polarity() for more detail.

**Instructions**

Calculate the polarity() of positive in a new object called pos_score. Encase the entire call in parentheses so the output is also printed.

```{r}
# Example statement
positive <- "DataCamp courses are good for learning"

# Calculate polarity of statement
(pos_score <-polarity(positive))
```


## Manually perform the same polarity calculation.

Get a word count object by calling counts() on the polarity object.
All the identified subjectivity words are part of count object's list. Specifically, positive words are in pos.words element vector. Find the number of positive words in n_good by calling length() on the first part of the pos.words element.
Capture the total number of words and assign it to n_words. This value is stored in pos_count as the wc element.
De-construct the polarity() calculation by dividing n_good by sqrt() of n_words. Compare the result to pos_pol to the equation's result.

```{r}
# From previous step
positive <- "DataCamp courses are good for learning"
pos_score <- polarity(positive)

# Get counts
(pos_counts <- counts(pos_score))
  
# Number of positive words
n_good <- length(pos_counts$pos.words[[1]])
  
# Total number of words
n_words <- pos_counts$wc
  
# Verify polarity score
n_good / sqrt(n_words)
```

## Happy songs!
Of course just positive and negative words aren't enough. In this exercise you will learn about valence shifters which tell you about the author's emotional intent. Previously you applied polarity() to text without valence shifters. In this example you will see amplification and negation words in action.

Recall that an amplifying word adds 0.8 to a positive word in polarity() so the positive score becomes 1.8. For negative words 0.8 is subtracted so the total becomes -1.8. Then the score is divided by the square root of the total number of words.

Consider the following example from Frank Sinatra:

"It was a very good year"
"Good" equals 1 and "very" adds another 0.8. So, 1.8/sqrt(6) results in 0.73 polarity.

A negating word such as "not" will inverse the subjectivity score. Consider the following example from Bobby McFerrin:

"Don't worry Be Happy"
"worry is now 1 due to the negation "don't." Adding the "happy", +1, equals 2. With 4 total words, 2 / sqrt(4) equals a polarity score of 1.

**Instructions**

Examine the conversation data frame,conversation. Note the valence shifters like "never" in the text column.
Apply polarity() to the text column of conversation to calculate polarity for the entire conversation.
Calculate the polarity scores by student, assigning the result to student_pol.
Call polarity() again, this time passing two columns of conversation.
The text variable is text and the grouping variable is student.
To see the student level results, use scores() on student_pol.
The counts() function applied to student_pol will print the sentence level polarity for the entire data frame along with lexicon words identified.
The polarity object, student_pol, can be plotted with plot().


```{r}
# Examine conversation
head(conversation)

# Polarity - All
polarity(conversation$text)

# Polarity - Grouped
student_pol <- conversation %$%
  polarity(text, student)

# Student results
scores(student_pol)

# Sentence by sentence
counts(student_pol)

# qdap plot
plot(student_pol)
```

## LOL, this song is wicked good
Even with Zipf's law in action, you will still need to adjust lexicons to fit the text source (for example twitter versus legal documents) or the author's demographics (teenager versus the elderly). This exercise demonstrates the explicit components of polarity() so you can change it if needed.

In Trey Songz "Lol :)" song there is a lyric "LOL smiley face, LOL smiley face." In the basic polarity() function, "LOL" is not defined as positive. However, "LOL" stands for "Laugh Out Loud" and should be positive. As a result, you should adjust the lexicon to fit the text's context which includes pop-culture slang. If your analysis contains text from a specific channel (Twitter's "LOL"), location (Boston's "Wicked Good"), or age group (teenagers' "sick") you will likely have to adjust the lexicon.

In this exercise you are not adjusting the subjectivity lexicon or qdap dictionaries containing valence shifters. Instead you are examining the existing word data frame objects so you can change them in the following exercise.

We've created text containing two excerpts from Beyoncé's "Crazy in Love" lyrics for the exercise.

**Instructions**

Print key.pol to see a portion of the subjectivity words and values.
Examine the predefined negation.words to print all the negating terms.
Now print the amplification.words to see the words that add values to the lexicon.
Check the deamplification.words to print the words that reduce the lexicon values.
Call text to see conversation.

```{r}
# Examine the key.pol
print(key.pol)

# Negators
print(negation.words)

# Amplifiers
print(amplification.words)

# De-amplifiers
print(deamplification.words)

# Examine
text
```

Calculate polarity() as follows.
Set text.var to text$words.
Set grouping.var to text$speaker.
Set polarity.frame to key.pol.
Set negators to negation.words.
Set amplifiers to amplification.words.
Set deamplifiers to deamplification.words.

```{r}

# Complete the polarity parameters
polarity(
  text.var       = text$words,
  grouping.var   = text$speaker,
  polarity.frame = key.pol,
  negators       = negation.words,
  amplifiers     = amplification.words,
  deamplifiers   = deamplification.words 
)
```


## Stressed Out!
Here you will adjust the negative words to account for the specific text. You will then compare the basic and custom polarity() scores.

A popular song from Twenty One Pilots is called "Stressed Out". If you scan the song lyrics, you will observe the song is about youthful nostalgia. Overall, most people would say the polarity is negative. Repeatedly the lyrics mention stress, fears and pretending.

Let's compare the song lyrics using the default subjectivity lexicon and also a custom one.

To start, you need to verify the key.pol subjectivity lexicon does not already have the term you want to add. One way to check is with grep(). The grep() function returns the row containing characters that match a search pattern. Here is an example used while indexing.

data_frame[grep("search_pattern", data_frame$column), ]
After verifying the slang or new word is not already in the key.pol lexicon you need to add it. The code below uses sentiment_frame() to construct the new lexicon. Within the code sentiment_frame() accepts the original positive word vector, positive.words. Next, the original negative.words are concatenated to "smh" and "kappa", both considered negative slang. Although you can declare the positive and negative weights, the default is 1 and -1 so they are not included below.

custom_pol <- sentiment_frame(positive.words, c(negative.words, "hate", "pain"))
Now you are ready to apply polarity and it will reference the custom subjectivity lexicon!

**Instructions**

We've created stressed_out which contains the lyrics to the song "Stressed Out", by Twenty One Pilots.

Use polarity() on stressed_out to see the default score.
Check key.pol for any words containing "stress". Use grep() to index the data frame by searching in the x column.
Create custom_pol as a new sentiment data frame.
Call sentiment_frame() and pass positive.words as the first argument without concatenating any new terms.
Next, use c() to combine negative.words with new terms "stressed" and "turn back".
Reapply polarity() to stressed_out with the additional parameter polarity.frame = custom_pol to compare how the new words change the score to a more accurate representation of the song.


```{r}
# stressed_out has been pre-defined
head(stressed_out)

# Basic lexicon score
polarity(stressed_out)

# Check the subjectivity lexicon
key.pol[grep("stress", x)]

# New lexicon
custom_pol <- sentiment_frame(positive.words, c(negative.words, "stressed", "turn back"))

# Compare new score
polarity(stressed_out, polarity.frame = custom_pol)
```

## DTM vs. tidytext matrix
The tidyverse is a collection of R packages that share common philosophies and are designed to work together. This chapter covers some tidy functions to manipulate data. In this exercise you will compare a DTM to a tidy text data frame called a tibble.

Within the tidyverse, each observation is a single row in a data frame. That makes working in different packages much easier since the fundamental data structure is the same. Parts of this course borrow heavily from the tidytext package which uses this data organization.

For example, you may already be familiar with the %>% operator from the magrittr package. This forwards an object on its left-hand side as the first argument of the function on its right-hand side.

In the example below, you are forwarding the data object to function1(). Notice how the parentheses are empty. This in turn is forwarded to function2(). In the last function you don't have to add the data object because it was forwarded from the output of function1(). However, you do add a fictitious parameter, some_parameter as TRUE. These pipe forwards ultimately create the object.

object <- data %>% 
           function1() %>%
           function2(some_parameter = TRUE)
To use the %>% operator, you don't necessarily need to load the magrittr package, since it is also available in the dplyr package. dplyr also contains the functions inner_join() (which you'll learn more about later) and count() for tallying data. The last function you'll need is mutate() to create new variables or modify existing ones.

object <- data %>%
  mutate(new_Var_name = Var1 - Var2)
or to modify a variable

object <- data %>%
  mutate(Var1 = as.factor(Var1))
You will also use tidyr's spread() function to organize the data with each row being a line from the book and the positive and negative values as columns.

index	negative	positive
42	2	0
43	0	1
44	1	0
To change a DTM to a tidy format use tidy() from the broom package.

tidy_format <- tidy(Document_Term_Matrix)
This exercise uses text from the Greek tragedy, Agamemnon. Agamemnon is a story about marital infidelity and murder. You can download a copy here.

**Instructions**

We've already created a clean DTM called ag_dtm for this exercise.

Create ag_dtm_m by applying as.matrix() to ag_dtm.
Using brackets, [ and ], index ag_dtm_m to row 2206.
Apply tidy() to ag_dtm. Call the new object ag_tidy.
Examine ag_tidy at rows [831:835, ] to compare the tidy format. You will see a common word from the examined part of ag_dtm_m in step 2.


```{r}
# As matrix
ag_dtm_m <- as.matrix(ag_dtm)

# Examine line 2206 and columns 245:250
ag_dtm_m[2206, 245:250]

# Tidy up the DTM
ag_tidy <- tidy(ag_dtm)

# Examine tidy with a word you saw
ag_tidy[831:835 , ]
```


## Getting Sentiment Lexicons
So far you have used a single lexicon. Now we will transition to using three, each measuring sentiment in different ways.

The tidytext package contains a function called get_sentiments which along with the [textdata] package allows you to download & interact well researched lexicons. Here is a small section of the loughran lexicon.

Word	Sentiment
abandoned	negative
abandoning	negative
abandonment	negative
abandonments	negative
abandons	negative
This lexicon contains 4150 terms with corresponding information. We will be exploring other lexicons but the structure & method to get them is similar.

Let's use tidytext with textdata to explore other lexicons' word labels!

**Instructions**

Use get_sentiments() to obtain the "afinn" lexicon, assigning to afinn_lex.
Review the overall count() of value in afinn_lex.

```{r}

# Subset to AFINN
afinn_lex <- get_sentiments("afinn")

# Count AFINN scores
afinn_lex %>% 
  count(value)
```

Do the same again, this time with the "nrc" lexicon. That is,
get the sentiments, assigning to nrc_lex, then
count the sentiment column, assigning to nrc_counts.

```{r}
# Subset to nrc
nrc_lex <- get_sentiments("nrc")

# Make the nrc counts object
nrc_counts <- nrc_lex %>%
  count(sentiment)

```

Create a ggplot labeling the y-axis as n vs. x-axis of sentiment.
Add a col layer using geom_col(). (This is like geom_bar(), but used when you've already summarized with count().)

```{r}

# From previous step
nrc_counts <- get_sentiments("nrc") %>% 
  count(sentiment)
  
# Plot n vs. sentiment
ggplot(nrc_counts, aes(x = sentiment, y = n)) +
  # Add a col layer
  geom_col() +
  theme_gdocs()
```


## Bing tidy polarity: Simple example
Now that you understand the basics of an inner join, let's apply this to the "Bing" lexicon. Keep in mind the inner_join() function comes from dplyr and the lexicon object is obtained using tidytext's get_sentiments() function'.

The Bing lexicon labels words as positive or negative. The next three exercises let you interact with this specific lexicon. To use get_sentiments() pass in a string such as "afinn", "bing", "nrc", or "loughran" to download the specific lexicon.

The inner join workflow:

Obtain the correct lexicon using get_sentiments().
Pass the lexicon and the tidy text data to inner_join().
In order for inner_join() to work there must be a shared column name. If there are no shared column names, declare them with an additional parameter, by equal to c with column names like below.
object <- x %>% 
    inner_join(y, by = c("column_from_x" = "column_from_y"))
Perform some aggregation and analysis on the table intersection.

**Instructions**

We've loaded ag_txt containing the first 100 lines from Agamemnon and ag_tidy which is the tidy version.

For comparison, use polarity() on ag_txt.
Get the "bing" lexicon by passing that string to get_sentiments().
Perform an inner_join() with ag_tidy and bing.
The word columns are called "term" in ag_tidy & "word" in the lexicon, so declare the by argument.
Call the new object ag_bing_words.
Print ag_bing_words, and look at some of the words that are in the result.
Pass ag_bing_words to count() of sentiment using the pipe operator, %>%. Compare the polarity() score to sentiment count ratio.


```{r}
# Qdap polarity
polarity(ag_txt)

# Get Bing lexicon
bing <- get_sentiments("bing")

# Join text to lexicon
ag_bing_words <- inner_join(ag_tidy, bing, by = c("term" = "word"))

# Examine
ag_bing_words

# Get counts by sentiment
ag_bing_words %>%
  count(sentiment)
```


## Bing tidy polarity: Count & spread the white whale
In this exercise you will apply another inner_join() using the "bing" lexicon.

Then you will manipulate the results with both count() from dplyr and spread() from tidyr to learn about the text.

The spread() function spreads a key-value pair across multiple columns. In this case the key is the sentiment & the values are the frequency of positive or negative terms for each line. Using spread() changes the data so that each row now has positive and negative values, even if it is 0.

**Instructions**

In this exercise, your R session has m_dick_tidy which contains the book Moby Dick and bing, containing the lexicon similar to the previous exercise.

Perform an inner_join() on m_dick_tidy and bing.
As before, join the "term" column in m_dick_tidy to the "word" column in the lexicon.
Call the new object moby_lex_words.
Create a column index, equal to as.numeric() applied to document. This occurs within mutate() in the tidyverse.
Create moby_count by forwarding moby_lex_words to count(), passing in sentiment, index.
Generate moby_spread by piping moby_count to spread() which contains sentiment, n, and fill = 0.


```{r}
# Inner join
moby_lex_words <- inner_join(m_dick_tidy, bing, by = c("term" = "word"))

moby_lex_words <- moby_lex_words %>%
  # Set index to numeric document
  mutate(index = as.numeric(document))

moby_count <- moby_lex_words %>%
  # Count by sentiment, index
  count(sentiment, index)

# Examine the counts
moby_count

moby_spread <- moby_count %>%
  # Spread sentiments
  spread(sentiment, n, fill = 0)

# Review the spread data
moby_spread
```

## Bing tidy polarity: Call me Ishmael (with ggplot2)!
The last Bing lexicon exercise! In this exercise you will use the pipe operator (%>%) to create a timeline of the sentiment in Moby Dick. In the end you will also create a simple visual following the code structure below. The next chapter goes into more depth for visuals.

ggplot(spread_data, aes(index_column, polarity_column)) +
  geom_smooth()
  
**Instructions**

Inner join moby to the bing lexicon.
Call inner_join() to join the tibbles.
Join by the term column in the text and the word column in the lexicon.
Count by sentiment and index.
Reshape so that each sentiment has its own column.
Call spread().
The key column (to split into multiple columns) is sentiment.
The value column (containing the counts) is n.
Also specify fill = 0 to fill out missing values with a zero.
Use mutate() to add the polarity column. Define it as the difference between the positive and negative columns.

```{r}

moby_polarity <- moby %>%
  # Inner join to lexicon
  inner_join(bing, by = c("term" = "word")) %>%
  # Count the sentiment scores
  count(sentiment, index) %>% 
  # Spread the sentiment into positive and negative columns
  spread(sentiment, n, fill = 0) %>%
  # Add polarity column
  mutate(polarity = positive-negative)
```

Using moby_polarity, plot polarity vs. index.
Add a smooth trend layer by calling geom_smooth() with no arguments.

```{r}
# From previous step
moby_polarity <- moby %>%
  inner_join(bing, by = c("term" = "word")) %>%
  count(sentiment, index) %>% 
  spread(sentiment, n, fill = 0) %>%
  mutate(polarity = positive - negative)
  
# Plot polarity vs. index
ggplot(moby_polarity, aes(x = index, y = polarity)) + 
  # Add a smooth trend curve
  geom_smooth()   
```

## AFINN: I'm your Huckleberry
Now we transition to the AFINN lexicon. The AFINN lexicon has numeric values from 5 to -5, not just positive or negative. Unlike the Bing lexicon's sentiment, the AFINN lexicon's sentiment score column is called value.

As before, you apply inner_join() then count(). Next, to sum the scores of each line, we use dplyr's group_by() and summarize() functions. The group_by() function takes an existing data frame and converts it into a grouped data frame where operations are performed "by group". Then, the summarize() function lets you calculate a value for each group in your data frame using a function that aggregates data, like sum() or mean(). So, in our case we can do something like

data_frame %>% 
    group_by(book_line) %>% 
    summarize(total_score = sum(book_line))
In the tidy version of Huckleberry Finn, line 9703 contains words "best", "ever", "fun", "life" and "spirit". "best" and "fun" have AFINN scores of 3 and 4 respectively. After aggregating, line 9703 will have a total score of 7.

In the tidyverse, filter() is preferred to subset() because it combines the functionality of subset() with simpler syntax. Here is an example that filter()s data_frame where some value in column1 is equal to 24. Notice the column name is not in quotes.

filter(data_frame, column1 == 24)
The afinn object contains the AFINN lexicon. The huck object is a tidy version of Mark Twain's Adventures of Huckleberry Finn for analysis.

Line 5400 is All the loafers looked glad; I reckoned they was used to having fun out of Boggs. Stopwords and punctuation have already been removed in the dataset.

**Instructions**

Run the code to look at line 5400, and see the sentiment scores of some words.
inner_join() huck to the afinn lexicon.
Remember huck is already piped into the function so just add the lexicon.
Join by the term column in the text and the word column in the lexicon.
Use count() with value and line to tally/count observations by group.
Assign the result to huck_afinn

```{r}
# See abbreviated line 5400
huck %>% filter(line == 5400)

# What are the scores of the sentiment words?
afinn %>% filter(word %in% c("fun", "glad"))

huck_afinn <- huck %>% 
  # Inner Join to AFINN lexicon
  inner_join(afinn, by = c("term" = "word")) %>%
  # Count by value and line
  count(value, line)
```

Get the total sentiment score by line forwarding huck_afinn to group_by() and passing line without quotes.
Create huck_afinn_agg using summarize(), setting total_value equal to the sum() of value * n.
Use filter() on huck_afinn_agg and line == 5400 to review a single line.

```{r}
# From previous step
huck_afinn <- huck %>% 
  inner_join(afinn, by = c("term" = "word")) %>%
  count(value, line)
  
huck_afinn_agg <- huck_afinn %>% 
  # Group by line
  group_by(line) %>%
  # Sum values times n (by line)
  summarize(total_value = sum(value*n))
  
huck_afinn_agg %>% 
  # Filter for line 5400
  filter(line == 5400)
```

Create a sentiment timeline. Pass huck_afinn_agg to the data argument of ggplot().
Then specify the x and y within aes() as line and total_value without quotes.
Add a layer with geom_smooth().

```{r}
# From previous steps
huck_afinn_agg <- huck %>% 
  inner_join(afinn, by = c("term" = "word")) %>%
  count(value, line) %>% 
  group_by(line) %>%
  summarize(total_value = sum(value * n))
  
# Plot total_value vs. line
ggplot(huck_afinn_agg, aes(x=line, y=total_value)) + 
  # Add a smooth trend curve
  geom_smooth() 
```

## The wonderful wizard of NRC
Last but not least, you get to work with the NRC lexicon which labels words across multiple emotional states. Remember Plutchik's wheel of emotion? The NRC lexicon tags words according to Plutchik's 8 emotions plus positive/negative.

In this exercise there is a new operator, %in%, which matches a vector to another. In the code below %in% will return FALSE, FALSE, TRUE. This is because within some_vec, 1 and 2 are not found within some_other_vector but 3 is found and returns TRUE. The %in% is useful to find matches.

some_vec <- c(1, 2, 3)
some_other_vector <- c(3, "a", "b")
some_vec %in% some_other_vector
Another new operator is !. For logical conditions, adding ! will inverse the result. In the above example, the FALSE, FALSE, TRUE will become TRUE, TRUE, FALSE. Using it in concert with %in% will inverse the response and is good for removing items that are matched.

!some_vec %in% some_other_vector
We've created oz which is the tidy version of The Wizard of Oz along with nrc containing the "NRC" lexicon with renamed columns.

**Instructions**

Inner join oz to the nrc lexicon.
Call inner_join() to join the tibbles.
Join by the term column in the text and the word column in the lexicon.
Filter to only Pluchik's emotions and drop the positive or negative words in the lexicon.
Use filter() to keep rows where the sentiment is not "positive" or "negative".
Group by sentiment.
Call group_by(), passing sentiment without quotes.
Get the total count of each sentiment.
Call summarize(), setting total_count equal to the sum() of count.
Assign the result to oz_plutchik.

```{r}
oz_plutchik <- oz %>% 
  # Join to nrc lexicon by term = word
  inner_join(nrc, by = c("term" = "word")) %>%
  # Only consider Plutchik sentiments
  filter(!sentiment %in% c("positive", "negative")) %>%
  # Group by sentiment
  group_by(sentiment) %>% 
  # Get total count by sentiment
  summarize(total_count = sum(count))
```


Create a bar plot with ggplot().
Pass in oz_plutchik to the data argument.
Then specify the x and y aesthetics, calling aes() and passing sentiment and total_count without quotes.
Add a column geom with geom_col(). (This is the same as geom_bar(), but doesn't summarize the data, since you've done that already.)

```{r}
# From previous step
oz_plutchik <- oz %>% 
  inner_join(nrc, by = c("term" = "word")) %>% 
  filter(!sentiment %in% c("positive", "negative")) %>%
  group_by(sentiment) %>% 
  summarize(total_count = sum(count))
  
# Plot total_count vs. sentiment
ggplot(oz_plutchik, aes(x = sentiment, y =total_count )) +
  # Add a column geom
  geom_col()
```

## Unhappy ending? Chronological polarity
Sometimes you want to track sentiment over time. For example, during an ad campaign you could track brand sentiment to see the campaign's effect. You saw a few examples of this at the end of the last chapter.

In this exercise you'll recap the workflow for exploring sentiment over time using the novel Moby Dick. One should expect that happy moments in the book would have more positive words than negative. Conversely dark moments and sad endings should use more negative language. You'll also see some tricks to make your sentiment time series more visually appealing.

Recall that the workflow is:

Inner join the text to the lexicon by word.
Count the sentiments by line.
Reshape the data so each sentiment has its own column.
(Depending upon the lexicon) Calculate the polarity as positive score minus negative score.
Draw the polarity time series.
This exercise should look familiar: it extends Bing tidy polarity: Call me Ishmael (with ggplot2)!.

**Instructions**

inner_join() the pre-loaded tidy version of Moby Dick, moby, to the bing lexicon.
Join by the "term" column in the text and the "word" column in the lexicon.
Count by sentiment and index.
Reshape so that each sentiment has its own column using spread() with the column sentiment and the counts column called n.
Also specify fill = 0 to fill out missing values with a zero.
Using mutate() add two columns: polarity and line_number.
Set polarity equal to the positive score minus the negative score.
Set line_number equal to the row number using the row_number() function.

```{r}
moby_polarity <- moby %>%
  # Inner join to the lexicon
  inner_join(bing, by = c("term"= "word")) %>%
  # Count by sentiment, index
  count(sentiment, index) %>%
  # Spread sentiments
  spread(sentiment, value = n, fill = 0) %>%
  mutate(
    # Add polarity field
    polarity = positive - negative,
    # Add line number field
    line_number = row_number()
  )
```


Create a sentiment time series with ggplot().
Pass in moby_polarity to the data argument.
Call aes() and pass in line_number and polarity without quotes.
Add a smoothed curve with geom_smooth().
Add a red horizontal line at zero by calling geom_hline(), with parameters 0 and "red".
Add a title with ggtitle() set to "Moby Dick Chronological Polarity".

```{r}
# From previous step
moby_polarity <- moby %>%
  inner_join(bing, by = c("term" = "word")) %>%
  count(sentiment, index) %>%
  spread(sentiment, n, fill = 0) %>%
  mutate(
    polarity = positive - negative,
    line_number = row_number()
  )
  
# Plot polarity vs. line_number
ggplot(moby_polarity, aes(x=line_number, y=polarity)) + 
  # Add a smooth trend curve
  geom_smooth() +
  # Add a horizontal line at y = 0
  geom_hline(yintercept = 0, color = "red") +
  # Add a plot title
  ggtitle("Moby Dick Chronological Polarity") +
  theme_gdocs()
```

## Word impact, frequency analysis
One of the easiest ways to explore data is with a frequency analysis. Although not difficult, in sentiment analysis this simple method can be surprisingly illuminating. Specifically, you will build a barplot. In this exercise you are once again working with moby and bing to construct your visual.

To get the bars ordered from lowest to highest, you will use a trick with factors. reorder() lets you change the order of factor levels based upon another scoring variable. In this case, you will reorder the factor variable term by the scoring variable polarity.

**Instructions**

Create moby_tidy_sentiment.
Use count() with term, sentiment, and wt = count.
Pipe to spread() with sentiment, n, and fill = 0.
Pipe to mutate(). Call the new variable polarity; calculated as positive minus negative.
Call moby_tidy_sentiment to review and compare it to the previous exercise.

```{r}

moby_tidy_sentiment <- moby %>% 
  # Inner join to bing lexicon by term = word
  inner_join(bing, by = c("term" = "word")) %>% 
  # Count by term and sentiment, weighted by count
  count(term, sentiment, wt = count) %>%
  # Spread sentiment, using n as values
  spread(sentiment, value = n, fill = 0) %>%
  # Mutate to add a polarity column
  mutate(polarity = positive - negative)

# Review
moby_tidy_sentiment
```


Use filter() on moby_tidy_sentiment to keep rows where the absolute polarity is greater than or equal to 50. abs() gives you absolute values.
mutate() a new vector pos_or_neg with an ifelse() function checking if polarity > 0 then declare the document "positive" else declare it "negative".

```{r}
# From previous step
moby_tidy_sentiment <- moby %>% 
  inner_join(bing, by = c("term" = "word")) %>% 
  count(term, sentiment, wt = count) %>%
  spread(sentiment, n, fill = 0) %>%
  mutate(polarity = positive - negative)

moby_tidy_pol <- moby_tidy_sentiment %>% 
  # Filter for absolute polarity at least 50 
  filter(abs(polarity) >= 50) %>% 
  # Add positive/negative status
  mutate(
    pos_or_neg = ifelse(polarity > 0, "positive", "negative")
  )
```

Using moby_tidy_pol, plot polarity vs. term, reordered by polarity (reorder(term, polarity)), filled by pos_or_neg.
Inside element_text(), rotate the x-axis text 90 degrees by setting angle = 90 and shifting the vertical justification with vjust = -0.1.

```{r}
# From previous steps
moby_tidy_pol <- moby %>% 
  inner_join(bing, by = c("term" = "word")) %>% 
  count(term, sentiment, wt = count) %>%
  spread(sentiment, n, fill = 0) %>%
  mutate(polarity = positive - negative) %>% 
  filter(abs(polarity) >= 50) %>% 
  mutate(
    pos_or_neg = ifelse(polarity > 0, "positive", "negative")
  )
  
# Plot polarity vs. (term reordered by polarity), filled by pos_or_neg
ggplot(moby_tidy_pol, aes(reorder(term, polarity), polarity, fill = pos_or_neg)) +
  geom_col() + 
  ggtitle("Moby Dick: Sentiment Word Frequency") + 
  theme_gdocs() +
  # Rotate text and vertically justify
  theme(axis.text.x = element_text(angle = 90, vjust = -0.1))
```


## Divide & conquer: Using polarity for a comparison cloud
Now that you have seen how polarity can be used to divide a corpus, let's do it! This code will walk you through dividing a corpus based on sentiment so you can peer into the information in subsets instead of holistically.

Your R session has oz_pol which was created by applying polarity() to "The Wonderful Wizard of Oz."

For simplicity's sake, we created a simple custom function called pol_subsections() which will divide the corpus by polarity score. First, the function accepts a data frame with each row being a sentence or document of the corpus. The data frame is subset anywhere the polarity values are greater than or less than 0. Finally, the positive and negative sentences, non-zero polarities, are pasted with parameter collapse so that the terms are grouped into a single corpus. Lastly, the two documents are concatenated into a single vector of two distinct documents.

pol_subsections <- function(df) {
  x.pos <- subset(df$text, df$polarity > 0)
  x.neg <- subset(df$text, df$polarity < 0)
  x.pos <- paste(x.pos, collapse = " ")
  x.neg <- paste(x.neg, collapse = " ")
  all.terms <- c(x.pos, x.neg)
  return(all.terms)
}
At this point you have omitted the neutral sentences and want to focus on organizing the remaining text. In this exercise we use the %>% operator again to forward objects to functions. After some simple cleaning use comparison.cloud() to make the visual.

**Instructions**

Extract the bits you need from oz_pol.
Call select(), declaring the first column text as text.var which is the raw text. The second column polarity should refer to the polarity scores polarity.
Now apply pol_subsections() to oz_df. Call the new object all_terms.
To create all_corpus apply VectorSource() to all_terms and then %>% to VCorpus().

```{r}
oz_df <- oz_pol$all %>%
  # Select text.var as text and polarity
  select(text = text.var, polarity = polarity)

# Apply custom function pol_subsections()
all_terms <- pol_subsections(oz_df)

all_corpus <- all_terms %>%
  # Source from a vector
  VectorSource() %>% 
  # Make a volatile corpus 
  VCorpus()
```


Create a term-document matrix, all_tdm, using TermDocumentMatrix() on all_corpus.
Add in the parameters control = list(removePunctuation = TRUE, stopwords = stopwords(kind = "en"))).
Then %>% to as.matrix() and %>% again to set_colnames(c("positive", "negative")).

```{r}
# From previous step
all_corpus <- oz_pol$all %>%
  select(text = text.var, polarity = polarity) %>% 
  pol_subsections() %>%
  VectorSource() %>% 
  VCorpus()
  
all_tdm <- TermDocumentMatrix(
  # Create TDM from corpus
  all_corpus,
  control = list(
    # Yes, remove the punctuation
    removePunctuation = T,
    # Use English stopwords
    stopwords = stopwords(kind = "en")
  )
) %>%
  # Convert to matrix
  as.matrix() %>%
  # Set column names
  set_colnames(c("positive", "negative"))
```


Apply comparison.cloud() to all_tdm with parameters max.words = 50, and colors = c("darkgreen","darkred").

```{r}
# From previous steps
all_tdm <- oz_pol$all %>%
  select(text = text.var, polarity = polarity) %>% 
  pol_subsections() %>%
  VectorSource() %>% 
  VCorpus() %>% 
  TermDocumentMatrix(
    control = list(
      removePunctuation = TRUE,
      stopwords = stopwords(kind = "en")
    )
  ) %>%
  as.matrix() %>%
  set_colnames(c("positive", "negative"))
  
comparison.cloud(
  # Create plot from the all_tdm matrix
  all_tdm,
  # Limit to 50 words
  max.words = 50,
  # Use darkgreen and darkred colors
  colors = c("darkgreen", "darkred")
)
```

## Emotional introspection
In this exercise you go beyond subsetting on positive and negative language. Instead you will subset text by each of the 8 emotions in Plutchik's emotional wheel to construct a visual. With this approach you will get more clarity in word usage by mapping to a specific emotion instead of just positive or negative.

Using the tidytext subjectivity lexicon, "nrc", you perform an inner_join() with your text. The "nrc" lexicon has the 8 emotions plus positive and negative term classes. So you will have to drop positive and negative words after performing your inner_join(). One way to do so is with the negation, !, and grepl().

The "Global Regular Expression Print Logical" function, grepl(), will return a True or False if a string pattern is identified in each row. In this exercise you will search for positive OR negative using the | operator, representing "or" as shown below. Often this straight line is above the enter key on a keyboard. Since the ! negation precedes grepl(), the T or F is switched so the "positive|negative" is dropped instead of kept.

Object <- tibble %>%
  filter(!grepl("positive|negative", column_name))
Next you apply count() on the identified words along with spread() to get the data frame organized.

comparison.cloud() requires its input to have row names, so you'll have to convert it to a base-R data.frame, calling data.frame() with the row.names argument.

**Instructions**

inner_join() moby to nrc.
Using filter() with a negation (!) and grepl() search for "positive|negative". The column to search is called sentiment.
Use count() to count by sentiment and term.
Reshape the data frame with spread(), passing in sentiment, n, and fill = 0.
Convert to plain data frame with data.frame(), making the term column into rownames.
Examine moby_tidy using head().

```{r}

moby_tidy <- moby %>%
  # Inner join to nrc lexicon
  inner_join(nrc, by = c("term" = "word")) %>% 
  # Drop positive or negative
  filter(!grepl("positive|negative", sentiment)) %>% 
  # Count by sentiment and term
  count(sentiment, term) %>% 
  # Spread sentiment, using n for values
  spread(sentiment, n, fill = 0)  %>% 
  # Convert to data.frame, making term the row names
  data.frame(row.names = "term")

# Examine
head(moby_tidy)
```


Using moby_tidy, draw a comparison.cloud().
Limit to 50 words.
Increase the title size to 1.5.

```{r}
# From previous step
moby_tidy <- moby %>%
  inner_join(nrc, by = c("term" = "word")) %>% 
  filter(!grepl("positive|negative", sentiment)) %>% 
  count(sentiment, term) %>% 
  spread(sentiment, n, fill = 0) %>% 
  data.frame(row.names = "term")
  
# Plot comparison cloud
comparison.cloud(moby_tidy, max.words = 50, title.size = 1.5)
```


## Compare & contrast stacked bar chart
Another way to slice your text is to understand how much of the document(s) are made of positive or negative words. For example a restaurant review may have some positive aspects such as "the food was good" but then continue to add "the restaurant was dirty, the staff was rude and parking was awful." As a result, you may want to understand how much of a document is dedicated to positive vs negative language. In this example it would have a higher negative percentage compared to positive.

One method for doing so is to count() the positive and negative words then divide by the number of subjectivity words identified. In the restaurant review example, "good" would count as 1 positive and "dirty," "rude," and "awful" count as 3 negative terms. A simple calculation would lead you to believe the restaurant review is 25% positive and 75% negative since there were 4 subjectivity terms.

Start by performing the inner_join() on a unified tidy data frame containing 4 books, Agamemnon, Oz, Huck Finn, and Moby Dick. Just like the previous exercise you will use filter() and grepl().

To perform the count() you have to group the data by book and then sentiment. For example all the positive words for Agamemnon have to be grouped then tallied so that positive words from all books are not mixed. Luckily, you can pass multiple variables into count() directly.

**Instructions**

Inner join all_books to the lexicon, nrc.
Filter to keep rows where sentiment contains "positive" or "negative". That is, use grepl() on the sentiment column, checking without the negation so that "positive|negative" are kept.
Count by book and sentiment.


```{r}
# Review tail of all_books
tail(all_books)

# Count by book & sentiment
books_sent_count <- all_books %>%
  # Inner join to nrc lexicon
  inner_join(nrc, by = c("term" = "word")) %>% 
  # Keep only positive or negative
  filter(grepl("positive|negative", sentiment)) %>% 
  # Count by book and by sentiment
  count(book, sentiment)
  
# Review entire object
books_sent_count
```

Group books_sent_count by line.
Mutate to add a column named percent_positive. This should e calculated as 100 times n divided by the sum of n.


```{r}
# From previous step
books_sent_count <- all_books %>%
  inner_join(nrc, by = c("term" = "word")) %>% 
  filter(grepl("positive|negative", sentiment)) %>% 
  count(book, sentiment)
  
book_pos <- books_sent_count %>%
  # Group by book
  group_by(book) %>% 
  # Mutate to add % positive column 
  mutate(percent_positive = 100 * n / sum(n) )
```

Using book_pos, plot percent_positive vs. book, using sentiment as the fill color.
Add a column layer with geom_col().


```{r}
# From previous steps
book_pos <- all_books %>%
  inner_join(nrc, by = c("term" = "word")) %>% 
  filter(grepl("positive|negative", sentiment)) %>% 
  count(book, sentiment) %>%
  group_by(book) %>% 
  mutate(percent_positive = 100 * n / sum(n))
  
# Plot percent_positive vs. book, filled by sentiment
ggplot(book_pos, aes(x = book, y = percent_positive, fill = sentiment)) +  
  # Add a col layer
  geom_col()
```

## Kernel density plot
Now that you learned about a kernel density plot you can create one! Remember it's like a smoothed histogram but isn't affected by binwidth. This exercise will help you construct a kernel density plot from sentiment values.

In this exercise you will plot 2 kernel densities. One for Agamemnon and another for The Wizard of Oz. For both you will perform an inner_join() with the "afinn" lexicon. Recall the "afinn" lexicon has terms scored from -5 to 5. Once in a tidy format, both books will retain words and corresponding scores for the lexicon.

After that, you need to row bind the results into a larger data frame using bind_rows() and create a plot with ggplot2.

From the visual you will be able to understand which book uses more positive versus negative language. There is clearly overlap as negative things happen to Dorothy but you could infer the kernel density is demonstrating a greater probability of positive language in the Wizard of Oz compared to Agamemnon.

We've loaded ag and oz as tidy versions of Agamemnon and The Wizard of Oz respectively, and created afinn as a subset of the tidytext "afinn" lexicon.

**Instructions**

Inner join ag to the lexicon, afinn, assigning to ag_afinn.
Do the same for The Wizard of Oz. This is the same code, but starting with the oz dataset and assigning to oz_afinn.
Use bind_rows() to combine ag_afinn to oz_afinn. Set the .id argument to "book" to create a new column with the name of each book.


```{r}
ag_afinn <- ag %>% 
  # Inner join to afinn lexicon
  inner_join(afinn, by = c("term" = "word"))

oz_afinn <- oz %>% 
  # Inner join to afinn lexicon
  inner_join(afinn, by = c("term" = "word"))

# Combine
all_df <- bind_rows(agamemnon = ag_afinn, oz = oz_afinn, .id = "book")
```

Using all_df, plot value, using book as the fill color.
Set the alpha transparency to 0.3.


```{r}
# From previous step
all_df <- bind_rows(
  agamemnon = ag %>% inner_join(afinn, by = c("term" = "word")), 
  oz = oz %>% inner_join(afinn, by = c("term" = "word")),
  .id = "book"
)

# Plot value, filled by book
ggplot(all_df, aes(value, fill = book)) + 
  # Set transparency to 0.3
  geom_density(alpha = 0.3) + 
  theme_gdocs() +
  ggtitle("AFINN Score Densities")
```

## Box plot
An easy way to compare multiple distributions is with a box plot. This code will help you construct multiple box plots to make a compact visual.

In this exercise the all_book_polarity object is already loaded. The data frame contains two columns, book and polarity. It comprises all books with qdap's polarity() function applied. Here are the first 3 rows of the large object.

book	polarity
14	huck	0.2773501
22	huck	0.2581989
26	huck	-0.5773503

This exercise introduces tapply() which allows you to apply functions over a ragged array. You input a vector of values and then a vector of factors. For each factor, value combination the third parameter, a function like min(), is applied. For example here's some code with tapply() used on two vectors.

f1 <- as.factor(c("Group1", "Group2", "Group1", "Group2"))
stat1 <- c(1, 2, 1, 2)
tapply(stat1, f1, sum)
The result is an array where Group1 has a value of 2 (1+1) and Group2 has a value of 4 (2+2).

**Instructions**

Since it's already loaded, examine the all_book_polarity with str().
Using tapply(), pass in all_book_polarity$polarity, all_book_polarity$book and the summary() function. This will print the summary statistics for the 4 books in terms of their polarity() scores. You would expect to see Oz and Huck Finn to have higher averages than Agamemnon or Moby Dick. Pay close attention to the median.
Create a box plot with ggplot() by passing in all_book_polarity.
Aesthetics should be aes(x = book, y = polarity).
Using a + add the geom_boxplot() with col = "darkred". Pay close attention to the dark line in each box representing median.
Next add another layer called geom_jitter() to add points for each of the words.


```{r}
# Examine
str(all_book_polarity)

# Summary by document
tapply(all_book_polarity$polarity, all_book_polarity$book, summary)

# Box plot
ggplot(all_book_polarity, aes(x = book, y = polarity)) +
  geom_boxplot(fill = c("#bada55", "#F00B42", "#F001ED", "#BA6E15"), col = "darkred") +
  geom_jitter(position = position_jitter(width = 0.1, height = 0), alpha = 0.02) +
  theme_gdocs() +
  ggtitle("Book Polarity")
```

## Radar chart
Remember Plutchik's wheel of emotion? The NRC lexicon has the 8 emotions corresponding to the first ring of the wheel. Previously you created a comparison.cloud() according to the 8 primary emotions. Now you will create a radar chart similar to the wheel in this exercise.

A radarchart is a two-dimensional representation of multidimensional data (at least 3). In this case the tally of the different emotions for a book are represented in the chart. Using a radar chart, you can review all 8 emotions simultaneously.

As before we've loaded the "nrc" lexicon as nrc and moby_huck which is a combined tidy version of both Moby Dick and Huck Finn.

In this exercise you once again use a negated grepl() to remove "positive|negative" emotional classes from the chart. As a refresher here is an example:

object <- tibble %>%
  filter(!grepl("positive|negative", column_name))
This exercise reintroduces spread() which rearranges the tallied emotional words. As a refresher consider this raw data called datacamp.

people	food	like
Nicole	bread	78
Nicole	salad	66
Ted	bread	99
Ted	salad	21
If you applied spread() as in spread(datacamp, people, like) the data looks like this.

food	Nicole	Ted
bread	78	99
salad	66	21

**Instructions**

Review moby_huck with tail().
inner_join() moby_huck and nrc.
Next, filter() negating "positive|negative" in the sentiment column. Assign the result to books_pos_neg.
After books_pos_neg is forwarded to group_by() with book and sentiment. Then tally() the object with an empty function.
Then spread() the books_tally by the book and n column.
Review the scores data.


```{r}
# Review tail of moby_huck
tail(moby_huck)

scores <- moby_huck %>% 
  # Inner join to lexicon
  inner_join(nrc, by = c("term" = "word")) %>% 
  # Drop positive or negative sentiments
  filter(!grepl("positive|negative", sentiment)) %>% 
  # Count by book and sentiment
  count(book, sentiment) %>% 
  # Spread book, using n as values
  spread(book, n)

# Review scores
scores
```

Call chartJSRadar() on scores which is an htmlwidget from the radarchart package.


```{r}
# From previous step
scores <- moby_huck %>% 
  inner_join(nrc, by = c("term" = "word")) %>% 
  filter(!grepl("positive|negative", sentiment)) %>% 
  count(book, sentiment) %>%
  spread(book, n)
  
# JavaScript radar chart
chartJSRadar(scores)
```

## Treemaps for groups of documents
Often you will find yourself working with documents in groups, such as author, product or by company. This exercise lets you learn about the text while retaining the groups in a compact visual. For example, with customer reviews grouped by product you may want to explore multiple dimensions of the customer reviews at the same time. First you could calculate the polarity() of the reviews. Another dimension may be length. Document length can demonstrate the emotional intensity. If a customer leaves a short "great shoes!" one could infer they are actually less enthusiastic compared to a lengthier positive review. You may also want to group reviews by product type such as women's, men's and children's shoes. A treemap lets you examine all of these dimensions.

For text analysis, within a treemap each individual box represents a document such as a tweet. Documents are grouped in some manner such as author. The size of each box is determined by a numeric value such as number of words or letters. The individual colors are determined by a sentiment score.

After you organize the tibble, you use the treemap library containing the function treemap() to make the visual. The code example below declares the data, grouping variables, size, color and other aesthetics.

treemap(
  data_frame,
  index = c("group", "individual_document"),
  vSize = "doc_length",
  vColor = "avg_score",
  type = "value",
  title = "Sentiment Scores by Doc",
  palette = c("red", "white", "green")
)
The pre-loaded all_books object contains a combined tidy format corpus with 4 Shakespeare, 3 Melville and 4 Twain books. Based on the treemap you should be able to tell who writes longer books, and the polarity of the author as a whole and for individual books.

**Instructions**

Calculate each book's length in a new object called book_length using count() with the book column.


```{r}
book_length <- all_books %>%
  # Count number of words per book
  count(book)
  
# Examine the results
book_length
```

Inner join all_books to the lexicon, afinn.
Group by author and book.
Use summarize() to calculate the mean_value as the mean() of value.
Inner join again, this time to book_length. Join by the book column.


```{r}
# From previous step
book_length <- all_books %>%
  count(book)
  
book_tree <- all_books %>% 
  # Inner join to afinn lexicon
  inner_join(afinn, by = c("term" = "word")) %>% 
  # Group by author, book
  group_by(author, book) %>%
  # Calculate mean book value
  summarize(mean_value = mean(value)) %>% 
  # Inner join by book
  inner_join(book_length, by = "book")

# Examine the results
book_tree
```

Draw a treemap, setting the following arguments.
Use the book_tree from the previous step.
Specify the aggregation index columns as "author" and "book".
Specify the vertex size column, vSize, as "n".
Specify the vertex color column, vColor, as "mean_value".
Specify a direct mapping from vColor to the palette by setting type = "value".


```{r}
# From previous steps
book_length <- all_books %>%
  count(book)
book_tree <- all_books %>% 
  inner_join(afinn, by = c("term" = "word")) %>% 
  group_by(author, book) %>%
  summarize(mean_value = mean(value)) %>% 
  inner_join(book_length, by = "book")

treemap(
  # Use the book tree
  book_tree,
  # Index by author and book
  index = c("author", "book"),
  # Use n as vertex size
  vSize = "n",
  # Color vertices by mean_value
  vColor = "mean_value",
  # Draw a value type
  type = "value",
  title = "Book Sentiment Scores",
  palette = c("red", "white", "green")
)
```

## Step 2: Identify Text Sources
In this short exercise you will load and examine a small corpus of property rental reviews from around Boston. Hopefully you already know read.csv() which enables you to load a comma separated file. In this exercise you will also need to specify stringsAsFactors = FALSE when loading the corpus. This ensures that the reviews are character vectors, not factors. This may seem mundane but the point of this chapter is to get you doing an entire workflow from start to finish so let's begin with data ingestion!

Next you simply apply str() to review the data frame's structure. It is a convenient function for compactly displaying initial values and class types for vectors.

Lastly you will apply dim() to print the dimensions of the data frame. For a data frame, your console will print the number of rows and the number of columns.

Other functions like head(), tail() or summary() are often used for data exploration but in this case we keep the examination short so you can get to the fun sentiment analysis!

**Instructions**

The Boston property rental reviews are stored in a CSV file located by the predefined variable bos_reviews_file.

Load the property reviews from bos_reviews_file with read.csv(). Call the object bos_reviews. Be sure to pass in the parameter stringsAsFactors = FALSE so the comments are not unique factors.
Examine the structure of the data frame using the base str() function applied to bos_reviews.
Find out how many reviews you are working with by calling dim() on the bos_reviews.


```{r}
# bos_reviews_file has been pre-defined
bos_reviews_file

# load raw text
bos_reviews <- read.csv(bos_reviews_file, stringsAsFactors = FALSE)

# Structure
str(bos_reviews)

# Dimensions
dim(bos_reviews)
```


## Quickly examine the basic polarity
When starting a sentiment project, sometimes a quick polarity() will help you set expectations or learn about the problem. In this exercise (to save time), you will apply polarity() to a portion of the comments vector while the larger polarity object is loaded in the background.

Using a kernel density plot you should notice the reviews do not center on 0. Often there are two causes for this sentiment "grade inflation." First, social norms may lead respondents to be pleasant instead of neutral. This, of course, is channel specific. Particularly snarky channels like e-sports or social media posts may skew negative leading to "deflation." These channels have different expectations. A second possible reason could be "feature based sentiment". In some reviews an author may write "the bed was comfortable and nice but the kitchen was dirty and gross." The sentiment of this type of review encompasses multiple features simultaneously and therefore could make an average score skewed.

In a subsequent exercise you will adjust this "grade inflation" but here explore the reviews without any change.

**Instructions**

Create practice_pol using polarity() on the first six reviews as in bos_reviews$comments[1:6]
Review the returned polarity object by calling practice_pol.
Call summary() on practice_pol$all$polarity - this will access the overall polarity for all 6 comments.
We've also loaded a larger polarity object for all 1000 comments. This new object is called bos_pol. Now apply summary() to the correct list element that returns all polarity scores of bos_pol.
The sample code has a barplot and kernel density plot almost ready to print. You must enter the data frame representing all scores. Hint: in the previous step, polarity represents a column of this data frame.


```{r}
# Practice apply polarity to first 6 reviews
practice_pol <- polarity(bos_reviews$comments[1:6])

# Review the object
practice_pol

# Check out the practice polarity
summary(practice_pol$all$polarity)

# Summary for all reviews
summary(bos_pol$all$polarity)

# Plot Boston polarity all element
ggplot(bos_pol$all, aes(x = polarity, y = ..density..)) + 
  geom_histogram(binwidth = 0.25, fill = "#bada55", colour = "grey60") +
  geom_density(size = 0.75) +
  theme_gdocs() 
```

## Create Polarity Based Corpora
In this exercise you will perform Step 3 of the text mining workflow. Although qdap isn't a tidy package you will mutate() a new column based on the returned polarity list representing all polarity (that's a hint BTW) scores. In chapter 3 we used a custom function pol_subsections which uses only base R declarations. However, in following the tidy principles this exercise uses filter() then introduces pull(). The pull() function works like works like [[ to extract a single variable.

Once segregated you collapse all the positive and negative comments into two larger documents representing all words among the positive and negative rental reviews.

Lastly, you will create a Term Frequency Inverse Document Frequency (TFIDF) weighted Term Document Matrix (TDM). Since this exercise code starts with a tidy structure, some of the functions borrowed from tm are used along with the %>% operator to keep the style consistent. If the basics of the tm package aren't familiar check out the Text Mining: Bag of Words course. Instead of counting the number of times a word is used (frequency), the values in the TDM are penalized for over used terms, which helps reduce non-informative words.

**Instructions**

Get the positive comments.
Mutate to add a polarity column, equal to bos_pol$all$polarity.
Filter to keep rows where polarity is greater than zero.
Use pull() to extract the comments column. (Pass this column without quotes.)
Collapse into a single string, separated by spaces using paste(), passing collapse = " ".


```{r}
pos_terms <- bos_reviews %>%
  # Add polarity column
  mutate(polarity = bos_pol$all$polarity) %>%
  # Filter for positive polarity
  filter(polarity > 0) %>%
  # Extract comments column
  pull(comments) %>% 
  # Paste and collapse
  paste(collapse = " ")
```

Do the same again, this time with negative comments.
Mutate to add a polarity column, equal to bos_pol$all$polarity.
Filter to keep rows where polarity is less than zero.
Extract the comments column.
Collapse into a single string, separated by spaces.


```{r}
neg_terms <- bos_reviews %>%
  # Add polarity column
  mutate(polarity = bos_pol$all$polarity) %>%
  # Filter for negative polarity
  filter(polarity < 0) %>%
  # Extract comments column
  pull(comments) %>%
  # Paste and collapse
  paste(collapse = " ")
```

Create a corpus of both positive and negative comments.
Use c() to concatenate pos_terms and neg_terms.
Source the text using VectorSource() without arguments.
Convert to a volatile corpus by calling VCorpus(), again without arguments.


```{r}
# From previous steps
pos_terms <- bos_reviews %>%
  mutate(polarity = bos_pol$all$polarity) %>%
  filter(polarity > 0) %>%
  pull(comments) %>% 
  paste(collapse = " ")
neg_terms <- bos_reviews %>%
  mutate(polarity = bos_pol$all$polarity) %>%
  filter(polarity < 0) %>%
  pull(comments) %>% 
  paste(collapse = " ")

# Concatenate the terms
all_corpus <- c(pos_terms, neg_terms) %>% 
  # Source from a vector
  VectorSource() %>% 
  # Create a volatile corpus
  VCorpus()
```


Create a term-document matrix from all_corpus.
Use term frequency inverse document frequency weighting by setting weighting to weightTfIdf.
Remove punctuation by setting removePunctuation to TRUE.
Use English stopwords by setting stopwords to stopwords(kind = "en").


```{r}
# From previous steps
pos_terms <- bos_reviews %>%
  mutate(polarity = bos_pol$all$polarity) %>%
  filter(polarity > 0) %>%
  pull(comments) %>% 
  paste(collapse = " ")
neg_terms <- bos_reviews %>%
  mutate(polarity = bos_pol$all$polarity) %>%
  filter(polarity < 0) %>%
  pull(comments) %>% 
  paste(collapse = " ")
all_corpus <- c(pos_terms, neg_terms) %>% 
  VectorSource() %>% 
  VCorpus()
  
all_tdm <- TermDocumentMatrix(
  # Use all_corpus
  all_corpus, 
  control = list(
    # Use TFIDF weighting
    weighting = weightTfIdf, 
    # Remove the punctuation
    removePunctuation = T,
    # Use English stopwords
    stopwords = stopwords(kind = "en")
  )
)

# Examine the TDM
all_tdm
```

## Create a Tidy Text Tibble!
Since you learned about tidy principles this code helps you organize your data into a tibble so you can then work within the tidyverse!

Previously you learned that applying tidy() on a TermDocumentMatrix() object will convert the TDM to a tibble. In this exercise you will create the word data directly from the review column called comments.

First you use unnest_tokens() to make the text lowercase and tokenize the reviews into single words.

Sometimes it is useful to capture the original word order within each group of a corpus. To do so, use mutate(). In mutate() you will use seq_along() to create a sequence of numbers from 1 to the length of the object. This will capture the word order as it was written.

In the tm package, you would use removeWords() to remove stopwords. In the tidyverse you first need to load the stop words lexicon and then apply an anti_join() between the tidy text data frame and the stopwords.

**Instructions**

Create tidy_reviews by piping (%>%) the original reviews object bos_reviews to the unnest_tokens() function. Pass in a new column name, word and declare the comments column. Remember in the tidyverse you don't need a $ or quotes.
Create a new variable the tidy way! Rewrite tidy_reviews by piping tidy_reviews to group_by with the column id. Then %>% it again to mutate(). Within mutate create a new variable original_word_order equal to seq_along(word).
Print out the tibble, tidy_reviews.
Load the premade "SMART" stopwords to your R session with data("stop_words").
Overwrite tidy_reviews by passing the original tidy_reviews to anti_join() with a %>%. Within anti_join() pass in the predetermined stop_words lexicon.


```{r}
# Vector to tibble
tidy_reviews <- bos_reviews %>% 
  unnest_tokens(word, comments)

# Group by and mutate
tidy_reviews <- tidy_reviews %>% 
  group_by(id) %>% 
  mutate(original_word_order = seq_along(word))

# Quick review
print(tidy_reviews)

# Load stopwords
data("stop_words")

# Perform anti-join
tidy_reviews_without_stopwords <- tidy_reviews %>% 
  anti_join(stop_words)
```

## Compare Tidy Sentiment to Qdap Polarity
Here you will learn that differing sentiment methods will cause different results. Often you will simply need to have results align directionally although the specifics may be different. In the last exercise you created tidy_reviews which is a data frame of rental reviews without stopwords. Earlier in the chapter, you calculated and plotted qdap's basic polarity() function. This showed you the reviews tend to be positive.

Now let's perform a similar analysis the tidytext way! Recall from an earlier chapter you will perform an inner_join() followed by count() and then a spread().

Lastly, you will create a new column using mutate() and passing in positive - negative.

**Instructions**

Using the get_sentiments() function with "bing" will obtain the bing subjectivity lexicon. Call the lexicon bing.
Since you already wrote this code in Chapter 2 simply enter in the lexicon object, bing, the new column name (polarity) and its calculation within mutate().
Lastly call summary() on the new object pos_neg. Although the values are different, are most rental reviews similarly positive compared to using polarity()? Do you see "grade inflation?"


```{r}
# Get the correct lexicon
bing <- get_sentiments("bing")

# Calculate polarity for each review
pos_neg <- tidy_reviews %>% 
  inner_join(bing) %>%
  count(sentiment) %>%
  spread(sentiment, n, fill = 0) %>% 
  mutate(polarity = positive - negative)

# Check outcome
summary(pos_neg)
```

## Assessing author effort
Often authors will use more words when they are more passionate. For example, a mad airline passenger will leave a longer review the worse (the perceived) service. Conversely a less impassioned passenger may not feel compelled to spend a lot of time writing a review. Lengthy reviews may inflate overall sentiment since the reviews will inherently contain more positive or negative language as the review lengthens. This coding exercise helps to examine effort and sentiment.

In this exercise you will visualize the relationship between effort and sentiment. Recall your rental review tibble contains an id and that a word is represented in each row. As a result a simple count() of the id will capture the number of words used in each review. Then you will join this summary to the positive and negative data. Ultimately you will create a scatter plot that will visualize author review length and its relationship to polarity.

**Instructions**

Calculate a measure of effort as the count of id.
Inner join to the polarity of each review, pos_neg.
Mutate to add a pol column. Use ifelse() to set pol to "Positive" if polarity is greater than or equal to zero, else "Negative".


```{r}
# Review tidy_reviews and pos_neg
tidy_reviews
pos_neg

pos_neg_pol <- tidy_reviews %>% 
  # Effort is measured as count by id
  count(id) %>% 
  # Inner join to pos_neg
  inner_join(pos_neg) %>% 
  # Add polarity status
  mutate(pol = ifelse(polarity >= 0, "Positive", "Negative"))

# Examine results
pos_neg_pol
```

Using pos_neg_pol, plot n vs. polarity, colored by pol.
Add a point layer using geom_point().
Add a smooth trend layer using geom_smooth().


```{r}
# From previous step
pos_neg_pol <- tidy_reviews %>% 
  count(id) %>% 
  inner_join(pos_neg) %>% 
  mutate(pol = ifelse(polarity >= 0, "Positive", "Negative"))
  
# Plot n vs. polarity, colored by pol
ggplot(pos_neg_pol, aes(polarity, n, color = pol)) + 
  # Add point layer
  geom_point(alpha = 0.25) +
  # Add smooth layer
  geom_smooth(method = "lm", se = FALSE) +
  theme_gdocs() +
  ggtitle("Relationship between word effort & polarity")
```

## Comparison Cloud
This exercise will create a common visual for you to understand term frequency. Specifically, you will review the most frequent terms from among the positive and negative collapsed documents. Recall the TermDocumentMatrix all_tdm you created earlier. Instead of 1000 rental reviews the matrix contains 2 documents containing all reviews separated by the polarity() score.

It's usually easier to change the TDM to a matrix. From there you simply rename the columns. Remember that the colnames() function is called on the left side of the assignment operator as shown below.

colnames(OBJECT) <- c("COLUMN_NAME1", "COLUMN_NAME2")
Once done, you will reorder the matrix to see the most positive and negative words. Review these terms so you can answer the conclusion exercises!

Lastly, you'll visualize the terms using comparison.cloud().

**Instructions**

Change the pre-loaded all_tdm to a matrix called all_tdm_m using as.matrix().
Use colnames() on all_tdm_m to declare c("positive", "negative").
Apply order() to all_tdm_m[,1] and set decreasing = TRUE.
Review the top 10 terms of the reordered TDM using pipe (%>%) then head() with n = 10.
Repeat the previous two steps with negative comments. Now you will order() by the second column, all_tdm_m[,2] and use decreasing = TRUE.
Review the 10 most negative terms indexing all_tdm_m by order_by_neg. Pipe this to head() with n = 10.


```{r}
# Matrix
all_tdm_m <- as.matrix(all_tdm)

# Column names
colnames(all_tdm_m) <- c("positive", "negative")

# Top pos words
order_by_pos <- order(all_tdm_m[, 1], decreasing = T)

# Review top 10 pos words
all_tdm_m[order_by_pos, ] %>% head(n = 10)

# Top neg words
order_by_neg <- order(all_tdm_m[, 2], decreasing = T)

# Review top 10 neg words
all_tdm_m[order_by_neg, ] %>% head(n = 10)
```

Draw a comparison.cloud() on all_tdm_m. Specify max.words equal to 20.



```{r}
# From previous step
all_tdm_m <- as.matrix(all_tdm)
colnames(all_tdm_m) <- c("positive", "negative")

comparison.cloud(
  # Use the term-document matrix
  all_tdm_m,
  # Limit to 20 words
  max.words = 20,
  colors = c("darkgreen","darkred")
)
```

## Scaled Comparison Cloud
Recall the "grade inflation" of polarity scores on the rental reviews? Sometimes, another way to uncover an insight is to scale the scores back to 0 then perform the corpus subset. This means some of the previously positive comments may become part of the negative subsection or vice versa since the mean is changed to 0. This exercise will help you scale the scores and then re-plot the comparison.cloud(). Removing the "grade inflation" can help provide additional insights.

Previously you applied polarity() to the bos_reviews$comments and created a comparison.cloud(). In this exercise you will scale() the outcome before creating the comparison.cloud(). See if this shows something different in the visual!

Since this is largely a review exercise, a lot of the code exists, just fill in the correct objects and parameters!

**Instructions**

Review a section of the pre-loaded bos_pol$all while indexing [1:6,1:3].
Add a new column to called scaled_polarity with scale() applied to the polarity score column bos_pol$all$polarity.
For positive comments, subset() where the new column bos_reviews$scaled_polarity is greater than (>) zero.
For negative comments, subset() where the new column bos_reviews$scaled_polarity is less than (<) zero.
Create pos_terms using paste() on pos_comments.
Now create neg_terms with paste() on neg_comments.
Organize the collapsed documents, pos_terms and neg_terms documents into a single corpus called all_terms.
Following the usual tm workflow by nesting VectorSource() inside VCorpus() applied to all_terms.
Make the TermDocumentMatrix() using the all_corpus object. Note this is a TfIdf weighted TDM with basic cleaning functions.
Change all_tdm to all_tdm_m using as.matrix(). Then rename the columns in the existing code to "positive" and "negative".
Finally! apply comparison.cloud() to the matrix object all_tdm_m. Take notice of the new most frequent negative words. Maybe it will uncover an unknown insight!



```{r}
# Review
bos_pol$all[1:6, 1:3]

# Scale/center & append
bos_reviews$scaled_polarity <- scale(bos_pol$all$polarity)

# Subset positive comments
pos_comments <- subset(bos_reviews$comments, bos_reviews$scaled_polarity > 0)

# Subset negative comments
neg_comments <- subset(bos_reviews$comments, bos_reviews$scaled_polarity < 0)

# Paste and collapse the positive comments
pos_terms <- paste(pos_comments, collapse = " ")

# Paste and collapse the negative comments
neg_terms <- paste(neg_comments, collapse = " ")

# Organize
all_terms<- c(pos_terms, neg_terms)

# VCorpus
all_corpus <- VCorpus(VectorSource(all_terms))

# TDM
all_tdm <- TermDocumentMatrix(
  all_corpus, 
  control = list(
    weighting = weightTfIdf, 
    removePunctuation = TRUE, 
    stopwords = stopwords(kind = "en")
  )
)

# Column names
all_tdm_m <- as.matrix(all_tdm)
colnames(all_tdm_m) <- c("positive", "negative")

# Comparison cloud
comparison.cloud(
  all_tdm_m, 
  max.words = 100,
  colors = c("darkgreen", "darkred")
)
```


## Confirm an expected conclusion
Refer to the following plot from the exercise "Comparison Cloud":



Its not surprising that the most common positive words for rentals included "walk", "restaurants", "subway" and "stations". In contrast, top negative terms included "condition", "dumpsters", "hygiene", "safety" and "sounds".

If you were looking to rent your clean apartment and it was close to public transit and good restaurants would it get a favorable review?

YES








