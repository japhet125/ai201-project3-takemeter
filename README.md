# TakeMeter: NBA Reddit Post Classifier

## Project Overview

TakeMeter is a text classification system for Reddit posts from r/nba. The goal is to automatically classify NBA discussion posts into four categories:

* analysis
* hot_take
* reaction
* news_information

The project compares a fine-tuned DistilBERT model against a zero-shot Groq baseline.

## Community Choice

I selected r/nba because it contains a diverse mixture of news reports, statistical analysis, emotional reactions, highlights, memes, and opinion-based discussions. This variety makes it a strong classification task because many posts share similar topics while differing in purpose and structure.

## Label Taxonomy

### analysis

Posts that use evidence, statistics, strategy, historical comparison, or logical reasoning to support a basketball claim.

Examples:

* "The Knicks are the first team to win the NBA Cup and NBA Finals in the same season."
* "Fox is catching way too much blame."

### hot_take

Posts that express a bold opinion, prediction, or judgment with limited supporting evidence.

Examples:

* "Bill Simmons: disappointing Wemby series."
* "Give me Wemby's raw emotion over the nonchalant superstar archetype any day."

### reaction

Posts that are primarily emotional responses, highlights, jokes, celebrations, quotes, memes, or fan moments.

Examples:

* "THE NEW YORK KNICKS ARE YOUR 2026 NBA CHAMPIONS!"
* "[Highlight] Brunson crossover on Wemby."

### news_information

Posts that primarily report factual information, announcements, transactions, injuries, contracts, or sourced reports.

Examples:

* "[Stein] Mavericks are actively exploring trade options."
* "Trae Young declines player option."

## Dataset

* Community: r/nba
* Total examples: 201
* Source: Public Reddit posts and comments

### Label Distribution

| Label            | Count |
| ---------------- | ----: |
| reaction         |    93 |
| news_information |    58 |
| analysis         |    30 |
| hot_take         |    20 |

## Fine-Tuning Approach

Base model:

* DistilBERT (distilbert-base-uncased)

Training configuration:

* Epochs: 3
* Learning rate: 2e-5
* Batch size: 16
* Weight decay: 0.01

The dataset was automatically split into:

* Train: 140
* Validation: 30
* Test: 31

## Baseline Model

The baseline used Groq llama-3.3-70b-versatile in a zero-shot setting.

The prompt defined each label and instructed the model to output only one valid label.

## Evaluation Results

### Baseline (Groq Zero-Shot)

Accuracy: **64.5%**

| Label            | Precision | Recall |   F1 |
| ---------------- | --------: | -----: | ---: |
| analysis         |      1.00 |   0.40 | 0.57 |
| hot_take         |      0.33 |   0.33 | 0.33 |
| reaction         |      0.65 |   0.79 | 0.71 |
| news_information |      0.67 |   0.67 | 0.67 |

### Fine-Tuned DistilBERT

Accuracy: **45.2%**

| Label            | Precision | Recall |   F1 |
| ---------------- | --------: | -----: | ---: |
| analysis         |      0.00 |   0.00 | 0.00 |
| hot_take         |      0.00 |   0.00 | 0.00 |
| reaction         |      0.46 |   0.93 | 0.62 |
| news_information |      0.33 |   0.11 | 0.17 |

### Comparison

The zero-shot Groq baseline substantially outperformed the fine-tuned DistilBERT model (64.5% vs. 45.2% accuracy). The fine-tuned model heavily favored the reaction class and struggled to correctly identify analysis and hot_take posts.

This result suggests that the dataset size (201 examples) and class imbalance limited the model's ability to learn the distinctions between labels. The baseline LLM already possessed strong knowledge of NBA discourse and therefore generalized better than the fine-tuned classifier.


### Baseline Results

Accuracy: 64.5%

| Label            | Precision | Recall |   F1 |
| ---------------- | --------: | -----: | ---: |
| analysis         |      1.00 |   0.40 | 0.57 |
| hot_take         |      0.33 |   0.33 | 0.33 |
| reaction         |      0.65 |   0.79 | 0.71 |
| news_information |      0.67 |   0.67 | 0.67 |

### Fine-Tuned Results

Accuracy: 45.2%

(Insert your fine-tuned classification report here from the notebook output.)

## Confusion Matrix

Insert the confusion matrix values from confusion_matrix.png as a markdown table.

## Error Analysis

### Error 1

Post:
"Bill Simmons: disappointing Wemby series"

True label:
hot_take

Predicted:
reaction

Analysis:
The model appears to associate player quotes and short statements with reaction posts. Because the post is short and lacks explicit opinion markers, the model failed to recognize the judgment-oriented nature of the statement.

### Error 2

Post:
"Fox is catching way too much blame"

True label:
analysis

Predicted:
reaction

Analysis:
The model likely focused on emotional language rather than the underlying argumentative structure. More analysis examples with conversational language would help.

### Error 3

Post:
"Post Game Thread Knicks vs Spurs Game 1"

True label:
news_information

Predicted:
reaction

Analysis:
Many game-thread examples resemble fan discussion and reaction content. The model struggled to distinguish event reporting from emotional discussion.

## Sample Classifications

| Post      | Predicted Label  | Confidence |
| --------- | ---------------- | ---------: |
| Example 1 | reaction         |       0.36 |
| Example 2 | reaction         |       0.34 |
| Example 3 | news_information |       0.31 |
| Example 4 | analysis         |       0.30 |

One correct prediction was reasonable because the post contained clear factual reporting language that matched the news_information category.

## Reflection

The model successfully learned the broad distinction between reaction posts and factual reporting. However, it struggled to separate analysis from reaction and hot_take from reaction.

The model appears to have overfit to emotional language and short post formats. Because reaction was the largest class, the model frequently defaulted to that category when uncertain.

## Spec Reflection

The project specification was helpful because it required defining labels before collecting data. This reduced ambiguity during annotation.

One way implementation diverged from the original plan was class balance. Although the goal was to collect a balanced dataset, the actual NBA subreddit contained significantly more reaction content than hot_take content.

## AI Usage

1. ChatGPT was used to stress-test label definitions and identify ambiguous examples before annotation.

2. ChatGPT was used during evaluation to identify patterns in misclassified examples and help organize the error analysis.

All final labels were manually reviewed and assigned by the author.
