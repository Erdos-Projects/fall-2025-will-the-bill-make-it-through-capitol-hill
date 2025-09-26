## Problem Definition

**Question**: What combination of bill text features, political variables, and external pressure indicators can predict congressional bill passage probability and timeline with sufficient accuracy to improve advocacy groups' resource allocation decisions compared to current heuristic-based approaches?

### Stakeholders

This project will be particularly relevant for non-governmental actors and advocacy groups deciding where, when, and how intensively to deploy limited lobbying resources. This analysis will enable them to allocate resources more effectively by providing predictions of bill passage probability and timeline, allowing them to concentrate efforts on winnable battles and optimal timing rather than relying on intuition-based resource allocation.

The primary stakeholders would include professional advocacy organizations like the ACLU, Chamber of Commerce, Sierra Club, Planned Parenthood, etc.
These groups are highly concerned about their ROI on lobbying spending, as well as the strategic timing of bill passage and coalition building with the appropriate political actors for their respective cause. Using a legislation success predictor, they can decide which of the many bills being filed in Congress to lobby versus monitor actively.
They can time major campaign launches and grassroots mobilization activities, and justify resource allocation to boards/donors. The main pain point that they would be solving is wasting resources on "lost causes" or missing winnable opportunities due to the deluge of information available regarding congressional activity.

Corporate government affairs departments in Fortune 500 companies, trade associations, and industry groups, as well as consulting/law firms specializing in government affairs, would also benefit from such a project. These actors are heavily invested in various activities pertaining to regulatory risk management, competitive advantage timing, and billable hour optimization.
These groups could use this product to quickly alert and advise clients and executives about the status of bills likely to impact their industry, plan counsel needs, and coordinate with industry coalitions, and price opportunities based on the likelihood of bill passage. Data-driven differentiation would create value for these types of firms.

### Unit of Analysis, Scope and Boundaries

The primary unit of analysis will be individual congressional bills at weekly intervals from introduction to final disposition. We will start by collecting bills from the previous two legislative cycles (2021-2022, 2023-2024) and track their progression through each chamber of Congress. House and Senate versions of bills will be treated separately as of now; amendments will be taken into account given their significance and timing.
We will include substantive legislation (not commemorative resolutions) and bills with identifiable policy impact and/or advocacy group interest. At this time, we will exclude the text of appropriations riders from our analysis as these are too complex and introduce different dynamics into the prediction problem. For a "real-time" prediction system, we will address automated data collection of bills from the current congressional session and assign passage probabilities from a model trained and tested on previous legislative cycles.

### Anti-Goals

- Not predicting WHY bills fail (causal mechanisms), only WHETHER and WHEN
- Not forecasting individual vote choices, only overall outcomes
- Not analyzing political strategy effectiveness, only outcome prediction
- Not recommending WHICH positions to take, only WHERE to focus resources
- Not predicting long-term policy impacts, only legislative success

## Data Gathering

### Congress.gov API: Bill Outcomes, Text, Sponsors, Passage Timeline
Extract bill text, sponsor info, topics, etc.

### ProPublica API + OpenSecrets websites: Lobbying

### News + Social Media Mentions

## Data Assessment


## Assessing Learnability


## KPI Definitions

Primary KPI
What metric directly reflects project success? (e.g., RMSE, accuracy, F1, uplift).
Secondary KPIs
Capture trade-offs: precision vs. recall, fairness metrics, latency, cost.
Baseline definition

