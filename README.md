# [Sponsorname] Contest

Unless otherwise discussed, this repo will be made public after contest completion, sponsor review, judging, and two-week issue mitigation window.

**Contributors to this repo:** prior to report publication, please review the [Agreements & Disclosures](/issues/1) issue.

---

## Contest findings are submitted to this repo

Typically most findings come in **on the last day of the contest**, so don't be alarmed at all if there's nothing here but crickets until the end of the contest.

As a sponsor, you have four critical tasks in the contest process:

1. Handle duplicate issues.
2. Weigh in on severity.
3. Respond to issues.
4. Share your mitigation of findings.

Let's walk through each of these.

## High and Medium Risk Issues

### Handle duplicates

Because wardens submit issues without seeing each other's submissions, there will always be findings that are clear duplicates. Other findings may use different language that ultimately describes the same issue, but from different angles. Use your best judgment in identifying duplicates, and don't hesitate to reach out (in your private contest channel) to ask C4 for advice.

1. For all issues labeled `3 (High Risk)` or `2 (Medium Risk)`, determine the best and most thorough description of the finding among the set of duplicates. (At least a portion of the content of the most useful description will be used in the audit report.)
2. Close the other duplicate issues and label them with `duplicate`
3. Mention the primary issue # when closing the issue (using the format `Duplicate of #issueNumber`), so that duplicate issues get linked.

**Note: QA and Gas reports do *not* need to be de-duped.** Please see the "QA and Gas reports" section below for more details.

### Weigh in on severity 

Judges have the ultimate discretion in determining severity of issues, as well as whether/how issues are considered duplicates. However, sponsor input is a significant criteria.

For a detailed breakdown of severity criteria and how to estimate risk, please refer to [the judging criteria in our documentation](https://docs.code4rena.com/roles/wardens/judging-criteria#estimating-risk-tl-dr).

If you disagree with a finding's severity, **leave the original severity label set by the warden and add the label** `disagree with severity`, along with a comment indicating your opinion for the judges to review. It is possible for issues to be considered `0 (Non-critical)`.

Feel free to use the `question` label for anything you would like additional C4 input on.

**Please don't change the severity labels;** that's up to the judge's discretion. 

### Respond to issues

Label each High or Medium risk finding as one of these:

- `sponsor confirmed`, meaning: "Yes, this is a problem and we intend to fix it."
- `sponsor disputed`, meaning either: "We cannot duplicate this issue" or "We disagree that this is an issue at all."
- `sponsor acknowledged`, meaning: "Yes, technically the issue is correct, but we are not going to resolve it for xyz reasons."

(Note: please *don't* use `sponsor disputed` for a finding if you think it should be considered of lower or higher severity. Instead, use the label `disagree with severity` and add comments to recommend a different severity level -- and include your reasoning.)

Add any necessary comments explaining your rationale for your evaluation of the issue. Note that when the repo is public, after all issues are mitigated, wardens will read these comments.

## QA and Gas Reports

For low and non-critical findings (AKA QA), as well as gas optimizations: all warden submissions in these three categories should now be submitted as bulk listings of issues and recommendations: 

- **[QA reports](https://docs.code4rena.com/roles/wardens/judging-criteria#qa-reports-low-non-critical)** should include *all* low severity and non-critical findings, along with a summary statement.
- **[Gas reports](https://docs.code4rena.com/roles/wardens/judging-criteria#gas-reports)** should include *all* gas optimization recommendations, along with a summary statement. 

For QA and Gas reports, we ask that you: 

- Leave a comment for the judge on any reports you consider to be particularly high quality. (These reports will be awarded on a curve.)
- Add the `sponsor disputed` label to any reports that you think should be *completely* disregarded by the judge, i.e. the report contains no valid findings at all.

## Once de-duping and labelling is complete

When you have marked all duplicates and labelled all findings, drop the C4 team a note in your private Discord backroom channel and let us know you've completed the sponsor review process. At this point, we will pass the repo over to the judge, and they'll get to work while you work on mitigation.  

## Share your mitigation of findings

*Note: this section does not need to be completed in order to move to the judging phase. You can continue work on mitigations while judging is occurring and even beyond that. Ultimately we won't publish the final audit report until you give us the ok.*

For each non-duplicate finding which you have confirmed, you will want to mitigate the issue before the contest report is made public.

As you undertake that process, we request that you take the following steps:

1. Within your own GitHub repo, create a pull request for each finding.
2. Link the PR to the issue that it resolves within your contest findings repo.

This will allow for complete transparency in showing the work of mitigating the issues found in the contest. **Do not close the issue;** simply label it as `resolved`. If the issue in question has duplicates, please link to your PR from the issue you selected as the best and most thoroughly articulated one.
