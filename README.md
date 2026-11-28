# Engagement of Fashion Posts on Tumblr

CS5113 Essentials of Data Science

Dr. Nidhi Goyal

Anandita Dakshayani Garimella, SE26MAID034

---

# Abstract

On tag-driven social media platforms like Tumblr, users rely heavily on metadata to find like-minded users, form communities and discover content based on shared interests. The aim of this project is to investigate whether posts belonging to different fashion subcultures on Tumblr receive different levels of engagement (notes). Data was collected from public Tumblr posts using its official API for five aesthetic tags between 1 July 2026 and 31 October 2026. The tags were selected from eight initial candidates based on posting volume, and the collected data was checked for gaps and daily posting consistency before being cleaned into 33,065 original posts. Engagement across the dataset is extremely skewed, with a median of 5 notes compared to a mean of 89, and some posts receiving as many as 52,287 notes. To investigate differences in engagement, a Kruskal-Wallis test was conducted, followed by Holm-corrected Mann–Whitney comparisons and additional robustness checks. The results show statistically significant differences in engagement between the five subcultures, with dark academia receiving the highest engagement and streetwear receiving the lowest (median of 0 notes). However, the differences between cottagecore, grunge and ootd are relatively small. The effect also becomes weaker when the influence of highly active blogs is controlled for. Overall, the findings suggest an association between fashion subcultures and post engagement, although the effect is relatively small to medium. These results are limited to the four-month sample and should not be interpreted as predictions of future fashion trends.

# 1. Introduction

## 1.1 Motivation

Fashion aesthetics spread online as tags and communities. Whether some aesthetics attract more engagement than others matters to creators deciding where to post and to anyone studying how online style communities form. Tumblr is a good place to study this because its tags are chosen by the author and its engagement is visible as a single count of notes (likes plus reblogs).

## 1.2 Research question and hypotheses

- **Research question:** Do different fashion subcultures (cores) get different engagement?
- **H<sub>0</sub> (null):** All five subcultures (cottagecore, dark academia, grunge, ootd, streetwear) get the same kind of engagement. Their log(1 + notes) distributions are the same.
- **H<sub>1</sub> (alternative):** At least one of the five subculture's log(1 + notes) distribution differs.

## 1.3 Contributions

- Self-collected dataset from Tumblr acquired using an official Tumblr API.
- Hypothesis test suited to skewed, zero-heavy engagement data, with effect sizes and robustness checks for the dependence that comes from reputed blogs.

## 1.4 Scope

The data covers four months on a single platform, Tumblr. Hence, this project cannot be used make any kind of forecast and does not represent fashion in general. It can only describe the engagement based on tags of posts.

# 2. Dataset

## 2.1 Source and collection

Public posts collected from Tumblr's API using pytumblr. The sample is every post the API returned for a tag.

Eight candidate tags collected in the same span of 1 July 2026 to 31 October 2026. Retained five of the tags with at least 30 collected posts per day. Selection was based on posting volume and data quality, not on engagement.

| Tag | Posts Collected | Posts per Day | Decision |
| --- | --- | --- | --- |
| cottagecore | 8248 | 67.1 | Kept |
| dark_academia | 8059 | 66.1 | Kept |
| grunge | 7018 | 57.6 | Kept |
| ootd | 6060 | 49.6 | Kept |
| streetwear | 3680 | 30.4 | Kept |
| vintage_fashion | 1122 | 9.1 | Dropped |
| y2k_fashion | 292 | 2.4 | Dropped |
| fashion | 0 | 0 | Dropped |

*Table 1: Candidate tags*

## 2.2 Cleaning and features

Posts were stripped of duplicates, restricted to the window, and blog names were replaced by salted SHA-256 hashes. No post showed any reblog indicator, so every post is original. A post tagged with more than one of the five tags appears once and records all its tags: 233 posts (0.7%) were in more than one tag and are excluded from group comparisons, leaving 32,832 single-tag posts.

The API's legacy fields turned out to be unreliable. By the legacy type field, 94.5% of posts look like text (31,241 of 33,065) and only 5.0% like photos, yet in ootd, for example, 6,092 of 6,097 posts use Tumblr's newer block format, 91% contain image markup and only 5 have a populated photo list. I therefore derived content features from the post HTML:

- **Media:** number of images, number of videos, and whether the post has any media.
- **Text:** text length and word count after stripping HTML, and number of links.
- **Tags and timing:** number of tags on the post, hour and weekday of posting, and post age in days (to a reference date of 21 November 2026, since notes accumulate with time).

## 2.3 Final dataset

33,065 original posts and 28 columns. Engagement (note_count) has a median of 5, a mean of 89.4, a standard deviation of 706.2 and a maximum of 52,287, and a quarter of posts have one note or fewer. There are no missing values. Blog identifiers are pseudonymised.

| Tag | Posts | Blogs | Median Notes | Mean notes | % of posts with 0 notes | % of posts with media |
| --- | --- | --- | --- | --- | --- | --- |
| cottagcore | 4466 | 1510 | 5.0 | 83.5 | 11.3 | 91.3 |
| dark_academia | 5839 | 1913 | 6.0 | 126.4 | 9.0 | 68.9 |
| grunge | 5519 | 2269 | 5.0 | 70.7 | 15.0 | 88.4 |
| ootd | 4804 | 1799 | 4.0 | 31.2 | 16.0 | 98.4 |
| streetwear | 1181 | 375 | 0.0 | 8.1 | 50.8 | 94.4 |

*Table 2: Descriptive Statistics by Tag*

## 2.4 Limits of the data

The sampling is tag based and so does not represent fashion or Tumblr as a whole. Tagging is noisy and the reliability of self-reported is questionable.

The candidate tags were chosen by judgment, not systematically, so the five tags studied are not a representative sample of fashion communities, and some (ootd, streetwear) are broader than a subculture.

# 3. Methodology

## 3.1 Variables

The outcome is the note count of the post. For confidence intervals and models log(1+notes) was used and +1 was used to handle posts with zero notes. The explanatory variable is the tag (subculture) and secondary variables are media (yes or no), number of tags, text length, number of links.

## 3.2 Tests or models

**Main test: Kruskal–Wallis.** H0: all five tags have the same distribution of engagement; H1: at least one differs. The data have four features that decide the test: engagement is heavily right-skewed. It is zero-heavy, there are five groups; and the groups have very different spreads.

# 4. Experiments

- **Software:** Python, scipy, matplotlib, pandas and numpy
- **Significance level (α):** 0.05
- **Random seed:** 42 for per blog cap, the balanced sample and the bootstrap.
- **Analysis variants:** uncapped, capped (main),

# 5. Results

## 5.1 Shape of data

From Figure 1, it is evident that the raw notes pile up near zero with a very long right tail.

The log transform helps, but the trend continues. The Q-Q plot deviates clearly from the expected straight line for normal data. Hence, we know that the data is not normal.

From Figure 2, shows the same pattern within each tag, with streetwear having the least number of notes.

*Figure 1. Distribution of engagement: raw notes, log(1+notes) and normal Q-Q plot.*

*Figure 2: Distribution of engagement by tag: raw notes, log(1+notes) with a normal curve and Q-Q plots*

## 5.2 Main Test

Table 4 shows the Kruskal–Wallis results in all four variants. H0 is always rejected. The effect size is about medium on uncapped data, falls to between small and medium once prolific blogs are controlled, and is largest in the balanced sample because that sample gives the small streetwear group the same weight as the others. Figure 3 shows the engagement of each tag.

| Variant | H (df = 4) | p | η²_H |
| --- | --- | --- | --- |
| Uncapped (32,832 posts) | 3,877.4 | < 0.001 | 0.118 |
| Capped, main (21,809 posts) | 1,194.9 | < 0.001 | 0.055 |
| Balanced (1,181 per tag) | 901.2 | < 0.001 | 0.152 |
| Blog level | 288.6 | < 0.001 | 0.036 |

*Table 3: Kruskal–Wallis test of log(1 + notes) across the five tags*

*Figure 3: Engagement (log(1 + notes)) by fashion subculture, capped data (outliers hidden).*

## 5.3 Pairwise comparisons

Table 4 lists the ten pairwise comparisons on the capped data. All are statistically significant, but the sizes differ greatly. Streetwear is clearly lowest (a random post from any other tag beats a streetwear post about 75–80% of the time), dark academia is moderately ahead of the middle group (54–57%), and cottagecore, grunge and ootd are separated by only 51–53%. Mean log engagement with 95% bootstrap intervals: dark academia 2.395 (2.348 to 2.439); cottagecore 2.137 (2.089 to 2.186); grunge 2.035 (1.991 to 2.079); ootd 1.917 (1.875 to 1.959); streetwear 0.772 (0.712 to 0.836).

| Tag a | Tag b | P(a > b) | Holm p |
| --- | --- | --- | --- |
| cottagecore | dark academia | 0.462 | < 0.001 |
| cottagecore | grunge | 0.520 | 0.0015 |
| cottagecore | ootd | 0.534 | < 0.001 |
| cottagecore | streetwear | 0.781 | < 0.001 |
| dark academia | grunge | 0.557 | < 0.001 |
| dark academia | ootd | 0.571 | < 0.001 |
| dark academia | streetwear | 0.803 | < 0.001 |
| grunge | ootd | 0.514 | 0.015 |
| grunge | streetwear | 0.758 | < 0.001 |
| ootd | streetwear | 0.749 | < 0.001 |

*Table 4: Pairwise Mann–Whitney comparisons (capped data, Holm-adjusted).*

# 6. Analysis and Conclusion

## 6.1 Interpretation

Engagement does differ by subculture, so H0 is rejected. The difference is real but modest apart from streetwear, and it is smaller than the uncapped analysis suggests: a good part of the apparent gap came from a few very active accounts.

**Streetwear.** Its low engagement survives the per-blog cap (median 0, about half the posts with no notes), so it is not just five blogs. Its posts come from only 375 blogs, and some of the most active behave like templated accounts (hundreds of posts with identical text length). I cannot tell from this data whether the low engagement reflects the aesthetic or the kinds of accounts that use the tag, for example shops. This should be read as a hypothesis, not a finding.

**Dark academia and the others.** Dark academia has the highest engagement and also the lowest media share (about 70%); one possible reading is a more text-oriented community, but the data do not test that. Cottagecore, grunge and ootd differ by only 1–3 percentage points in the probability that one post beats another, so with several thousand posts per tag, statistical significance there says little about practical importance.

**Correlates.** Posts with more tags tend to get more notes, which fits the idea that tags help posts be found, but this is an association. Post age is positively related to notes, so older posts have had longer to collect them. Median post age is similar across tags (about 76–89 days), so age is unlikely to explain the tag differences.

## 6.2 Limitations

- Tag-based sampling and volume-based tag selection: the results describe fairly popular aesthetics on one platform and do not represent fashion or Tumblr as a whole.
- Posts from the same blog are correlated. The cap and the blog-level analysis reduce this but do not remove it, and the cap changes the target population from all posts to a typical blog's posts.
- No follower counts, so blog size is an unmeasured confounder; notes are a snapshot at collection time.

## 6.3 Conclusion and future work

The aim of this project was to see if metadata specifically tag of a post is associated with differences in engagement it receives. Based on the self-collected sample of 33,065 Tumblr posts across five fashion tags corresponding to different subcultures, the null hypothesis of equal engagement across subcultures was rejected (Kruskal–Wallis, p < 0.001). Dark academia exhibits the most engagement, and streetwear the least.

However, these findings should be interpreted in light of several limitations. First, the study is restricted to Tumblr, meaning that the observed engagement patterns may reflect differences in platform demographics and user activity rather than the relative popularity of the subcultures themselves. Individuals associated with certain aesthetics may be more active on Tumblr, while others may gravitate towards platforms such as Instagram, Pinterest or TikTok. Consequently, engagement on a single platform may not provide a representative measure of the broader interest in, or cultural significance of, a particular subculture. Although the sample is relatively large, it represents a collected snapshot of posts and may not account for temporal variations in engagement or changes in platform activity.

# 7. Ethics and Reproducibility

- **Ethics:** All of the data is collected from the publicly available Tumblr posts using an API. All of the Blog names were anonymized by hashing their usernames to ensure the privacy of the posters.
- **Reproducibility:** fixed random seed (42)
- **Existing code and tools used:** pytumblr, pandas, numpy, matplotlib and scipy.
- **AI-assistance:** used Claude to research on types of tests that work with data that doesn't follow a normal distribution.
