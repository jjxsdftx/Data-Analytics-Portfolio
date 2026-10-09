# Korea COVID-19 AI Dashboard

An interactive Streamlit data visualization dashboard based on the DS4C dataset, covering the national outbreak overview, spatiotemporal spread, transmission chains, patient profiles, and social and environmental impacts.

## Project Access

- **GitHub repository:** [github.com/jjxsdftx/korea-covid19-dashboard](https://github.com/jjxsdftx/korea-covid19-dashboard)
- **Original Streamlit application:** [Open the Korea COVID-19 AI Dashboard](https://korea-covid19-dashboard-shfhrdk5jvg2mqx92bvgf8.streamlit.app/)
- **Static showcase:** [Open the Korea COVID-19 Intelligence Dashboard](https://korea-covid19-intelligence.peachylily8.chatgpt.site)

> The Streamlit link is the cloud deployment of this repository's original `main.py`. The static showcase uses the same DS4C data and serves as a portfolio-ready fallback that does not require a Python backend.

### Run Locally

```bash
git clone https://github.com/jjxsdftx/korea-covid19-dashboard.git
cd korea-covid19-dashboard
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
streamlit run main.py
```

Then open [http://localhost:8501](http://localhost:8501) in your browser. The main dashboard works without an API key. To use the AI Summary features, set the `OPENAI_API_KEY` environment variable separately.

---

### 1. Dashboard Structure

**Topic:** A panoramic analysis of South Korea's COVID-19 outbreak and control measures

**Target audience:** Public-health researchers, government decision-makers, and the general public

**Core narrative:** What happened (overview) -> Where it happened (time and space) -> Who was infected (population) -> How it spread (transmission) -> What external factors mattered (environment and behavior).

**Core concept:** Data-Driven Evidence + AI-Powered Insight

Built on Kaggle's well-known [DS4C (Data Science for COVID-19)](https://www.kaggle.com/kimjihoo/coronavirusdataset) dataset, this project delivers an interactive data visualization dashboard. Unlike a traditional static report, it integrates **OpenAI GPT-4o** to create a closed loop between data visualization and intelligent attribution analysis.

---

### 2. Key Features

- **Drill-down:** Users can click a province on the map, such as Daegu, and automatically filter the dashboard's other charts—including new-case trends and infection-source distributions—to that province. This interaction uses the hierarchy shared by `Region.csv` and `TimeProvince.csv`.

- **Filters:** Global filters for date range, gender, and infection status are placed at the top of the dashboard.

- **Color palette:** Visualization colors follow public-health conventions: red for confirmed cases or danger, green for recovery or safety, and black for deaths.

- **AI interpretation:** Users actively trigger an AI interaction by clicking the **AI Summary** button.

  1. **User interaction:** The user selects **Seoul** and **March 2020** in the visualization area.
  2. **Data filtering:** The Python backend filters the DataFrame and creates a `df_filtered` subset.
  3. **Chart rendering:** Plotly generates charts from `df_filtered`.
  4. **Prompt construction:** Python extracts key statistics—maximum values, trends, and anomalies—from `df_filtered` and inserts them into a prompt template. The full CSV is never sent to the API. Pandas first computes aggregations, and only key indicators such as the top five values, totals, and averages are sent to GPT. The prompt also defines a consistent expert role: `{"role": "system", "content": "You are a rigorous public-health data expert. Use objective language and avoid creating panic."}`
  5. **GPT generation:** The API returns a natural-language analysis.
  6. **Frontend presentation:** The generated explanation appears below or beside the corresponding chart.
  7. **Intelligent attribution:** The model combines government interventions from `Policy.csv`, such as a Level 4 Alert, with trend turning points to explain possible drivers of change.

---

### 3. Technology Stack

- **Language:** Python
- **Frontend framework:** Streamlit
- **Visualization:** Plotly Express, Plotly Dash, and Plotly Graph Objects
- **AI layer:** OpenAI API (GPT-4o)
- **Data processing:** Pandas and NumPy
- **Network analysis:** NetworkX for super-spreader analysis

---

### 4. Project Directory Structure

```text
DS4C-AI-Dashboard/
├── .env                    # Stores the OpenAI API key; never upload this file to GitHub
├── .gitignore              # Excludes .env, venv, and __pycache__
├── requirements.txt        # Project dependencies: streamlit, pandas, plotly, openai, etc.
├── README.md               # Project documentation and the core portfolio description
├── main.py                 # Streamlit entry point; handles navigation and page assembly
├── config.py               # Global configuration: page title, color theme, and constants
├── data/
│   ├── raw/                # Original Kaggle CSV files: Time.csv, Case.csv, etc.
│   └── processed/          # Cleaned and intermediate datasets for more complex ETL flows
├── utils/
│   ├── __init__.py
│   ├── data_loader.py      # Data layer: reads, cleans, and caches CSV files with @st.cache_data
│   ├── llm_helper.py       # AI layer: wraps OpenAI API calls and prompt templates
│   └── viz_helper.py       # Visualization layer: reusable Plotly functions and consistent styling
└── modules/                # Business layer: independent logic for each dashboard module
    ├── __init__.py
    ├── overview.py         # Module 1: outbreak overview dashboard
    ├── time_space.py       # Module 2: spatiotemporal evolution
    ├── transmission.py     # Module 3: transmission pathways
    ├── patient.py          # Module 4: patient profiles
    └── impact.py           # Module 5: environment and behavior
```

---

### 5. Visualization Module Design

#### Module 1: Outbreak Overview and KPIs

**Objective:** Let users understand the most important outbreak indicators at a glance.

- **Data source:** `Time.csv`, using cumulative values from the latest date

- **Visualizations:**

  - **KPI scorecards:**
    - **Card 1: Total Confirmed**
      - Value: latest `confirmed`
      - Change: latest `new_confirmed`
    - **Card 2: Active Cases — the most important indicator**
      - Value: calculated `active`
      - Meaning: the number of people currently hospitalized or isolated, reflecting pressure on the healthcare system
      - Change: `active_diff`, with red indicating an increase and green indicating a decrease
    - **Card 3: Recovered**
      - Value: `released`
      - Change: new recoveries from the previous day
    - **Card 4: Deceased**
      - Value: `deceased`
      - Change: new deaths from the previous day

  - **Testing funnel:** Total tests -> negative results -> confirmed cases. This view shows testing coverage and positivity, helping assess whether South Korea's widely recognized mass-testing strategy was effective.
    - **Data source:** `test`, `negative`, and `confirmed` in `Time.csv`
    - **Chart A: Testing Results Funnel**
      - Top layer: Total Tests (`test`)
      - Middle layer: Negative Results (`negative`)
      - Bottom layer: Confirmed Cases (`confirmed`)
      - Note: `test - negative - confirmed` represents tests still in progress or awaiting results.
    - **Chart B: Positivity Rate Trend**
      - Formula: `Positivity Rate = (confirmed / test) * 100`
      - Interpretation: A high positivity rate may indicate insufficient testing focused mainly on severe cases. A low rate—characteristic of South Korea's early response—suggests broad testing coverage.
      - Visualization: A line chart with a 5% reference line based on the WHO warning threshold.

  - **Dual-axis chart:** Shows both outbreak speed and cumulative scale.
    - **Data source:** `Time.csv`
    - **X-axis:** `date`
    - **Left Y-axis, bars:** Daily New Confirmed
      - Purpose: Make outbreak peaks immediately visible, such as the surge associated with the Daegu church cluster.
      - Color: light red
    - **Right Y-axis, line:** Accumulated Confirmed
      - Purpose: Show growth in overall scale. An S-shaped curve suggests that the outbreak is being brought under control.
      - Color: dark red
    - **Interactions:**
      - **Range slider:** Lets users zoom into a period such as the February–March outbreak.
      - **Policy markers:** An advanced feature that places markers on key dates and displays `gov_policy` content from `Policy.csv`, such as "School Closure," on hover.

  - **Severity analysis:**
    - **Data source:** `deceased` and `confirmed` in `Time.csv`
    - **Chart:** Case Fatality Rate trend
      - Formula: `(deceased / confirmed) * 100`
      - Presentation: A single line over time
      - Insight: CFR may rise early because of healthcare-system overload, then fall as clinical treatment improves.

  - **AI Summary design:**
    - **Trigger:** An **AI Summary** area at the bottom of the module. Clicking the button asks generative AI to analyze the current view.
    - **Prompt logic:** The Python backend asks the model to interpret the data selected on the dashboard in approximately 100–200 words.

#### Module 2: Spatiotemporal Analysis

**Design objective:** Combine time and space to show how the outbreak shifted from the Daegu/Gyeongsangbuk-do epicenter toward the Seoul/Gyeonggi capital region.

---

##### 1. Data Preparation

- **Core data sources:**
  - `TimeProvince.csv`: Daily `confirmed`, `released`, and `deceased` values for each province
  - `Region.csv`: Geographic coordinates and demographic attributes such as `elementary_school_count` and `elderly_population_ratio`
- **Pandas processing logic:**

```python
# 1. Merge datasets
# Join TimeProvince and Region on the province field to obtain coordinates.
df_map = pd.merge(df_time_province, df_region[['province', 'latitude', 'longitude']], on='province')

# 2. Calculate daily new cases because TimeProvince contains cumulative values.
# Compute the difference within each province.
df_map['new_confirmed'] = df_map.groupby('province')['confirmed'].diff().fillna(0)

# 3. Handle the start date and missing coverage.
# Detailed provincial records in TimeProvince begin on February 21.
df_map = df_map[df_map['date'] >= '2020-02-21']
```

---

##### 2. Core Visualization: Dynamic Geographic Distribution

- **Chart A: Animated Bubble Map**
  - **Library:** Plotly Express (`px.scatter_mapbox`)
  - **Basemap:** South Korea using Carto Positron or OpenStreetMap
  - **Logic:**
    - **Location:** `latitude` and `longitude` from `Region.csv`
    - **Bubble size:** `confirmed` for cumulative cases or `new_confirmed` for daily new cases; a toggle between the two is recommended
    - **Bubble color:** `province` for categorical coloring or confirmed-case values for a severity gradient in which darker red indicates greater severity
    - **Animation frame:** `date`
  - **Interaction:** When the user clicks Play, the Daegu bubble grows sharply first. Several months later, the Seoul bubble begins to expand, clearly illustrating the geographic shift in the outbreak.

- **Chart B: Choropleth Map — optional advanced view**
  - If a South Korean GeoJSON file is available, `px.choropleth_mapbox` provides a more professional result by filling administrative polygons directly rather than representing provinces only as bubbles.

---

##### 3. Supporting Visualization: Regional Outbreak Heatmap

Maps are intuitive, but they do not make it easy to identify the exact date on which each province peaked. A heatmap solves this problem.

- **Chart type:** Rectangular heatmap
- **X-axis:** `date`
- **Y-axis:** `province`, ordered by total confirmed cases with Daegu at the top
- **Color intensity:** `new_confirmed`
- **Insights:**
  - The Daegu row contains a dark-red block in late February corresponding to the Shincheonji Church event.
  - The Seoul row contains intermittent light-red blocks for smaller outbreaks such as Itaewon clubs and the call center cluster.

---

##### 4. In-Depth Analysis: Regional Comparison and Attribution

Users can select two provinces for direct comparison.

- **Chart:** Multi-line provincial trend comparison
- **Interaction:** Select "Seoul" and "Daegu" from dropdown menus.
- **Data normalization:**
  - **Absolute comparison:** For example, 100 cases in Seoul versus 1,000 in Daegu
  - **Per-capita comparison:** Use supporting fields in `Region.csv`, such as `elderly_population_ratio` and `nursing_home_count`, or supplement the data with provincial population figures to calculate cases per million people.
  - If population data cannot be added, `academy_ratio` can provide a contextual comparison, such as exploring why younger infection rates may have been elevated in Seoul's Gangnam-gu.

---

##### 5. AI Geographical Insight

In this module, the AI acts as a **geographic detective**.

- **Scenario:** The user clicks **Daegu** and pauses the timeline on **2020-02-29**.
- **Context retrieval:**
  1. Retrieve Daegu's confirmed-case count for that date from `TimeProvince.csv`.
  2. Query `Case.csv` for clusters where `province == 'Daegu'`.
     - The main `infection_case` is identified as **Shincheonji Church**.
  3. Retrieve Daegu's characteristics from `Region.csv`, such as whether it has a high elderly-population ratio.
- **Prompt construction:**

  > "The user is examining Daegu in late February. The data shows that this region accounted for more than 70% of national confirmed cases. Related records identify Shincheonji Church as the primary infection source. Explain why Daegu became the outbreak epicenter, considering its location in southeastern South Korea and the characteristics of this cluster."

- **Example AI response:**

  > "Daegu's late-February anomaly was driven mainly by a **super-spreading event**. Although Daegu is a major city in southeastern South Korea, the outbreak was concentrated within the specific and relatively closed Shincheonji Church community. The `Case` data indicates that a single church cluster drove exponential transmission rather than broad, random community spread."

---

##### 6. Additional Idea: High-Risk Location Map

Use the coordinates in `Case.csv`.

- **Chart:** Static scatter map
- **Content:** Mark all group-infection locations on the map.
- **Color:** Classify locations by `infection_case` type, such as church, hospital, or school.
- **Purpose:** This supporting view shows that even when an entire province appears highly affected, transmission may have originated from a small number of specific locations.

---

**Important note:** `Region.csv` is primarily city- and district-level data, such as Seoul–Gangnam-gu, while `TimeProvince.csv` contains province-level data, such as Seoul.

- When joining the tables, filter `Region.csv` to rows representing provincial centers—often rows where `city` matches `province`, or where `city` is the provincial capital. Alternatively, define a manual `Province -> Lat/Lon` mapping in `config.py` for more robust behavior.

#### Module 3: Transmission Pathways and Cluster Analysis

**Core questions:** Where did the virus come from, and how did it spread from one person to another?

##### 1. Data Mapping

This module connects macro-level events with individual-level transmission records.

- **`Case.csv` — macro-level clusters:**
  - **Key fields:** `infection_case` for the source name, such as Shincheonji Church; `group` for whether the event was a cluster; `confirmed` for the number of cases associated with the cluster; and `latitude/longitude` for location coordinates
  - **Purpose:** Analyze the main outbreak hotspots
- **`PatientInfo.csv` — individual-level transmission:**
  - **Key fields:** `patient_id`, `infected_by` as the source-patient ID and core relationship field, `infection_case`, and `contact_number`
  - **Purpose:** Build person-to-person transmission networks

---

##### 2. Core Visualization Design

##### Chart A: Infection Source Treemap

**Objective:** Immediately reveal which locations or events contributed most to the outbreak.

- **Chart type:** Treemap or sunburst chart
- **Data source:** `Case.csv`
- **Hierarchy:**
  - **Level 1:** `province`, to reveal regional differences in infection-source composition
  - **Level 2:** `infection_case`, such as Shincheonji Church, Call Center, or Gym
- **Area:** `confirmed`
- **Color:** `group`
  - **True, dark color:** Mass or cluster infection
  - **False, light color:** Sporadic, individual, or contact transmission
- **Interaction:** Clicking Daegu reveals a dominant Shincheonji Church block. Clicking Seoul reveals a more fragmented distribution including the Call Center and Itaewon Clubs.

##### Chart B: Super-Spreader Network Graph

**Objective:** Reconstruct transmission chains and identify super-spreaders through an advanced network visualization.

- **Chart type:** Network graph or node-link diagram
- **Libraries:** NetworkX for layout computation and Plotly for rendering
- **Data processing:**
  - Use `PatientInfo.csv`.
  - **Nodes:** `patient_id`
  - **Edges:** Directed from `infected_by` to `patient_id`
  - **Filtering:** Because the dataset is large, display only nodes with `contact_number > 10`, or connected subgraphs where `infected_by` is known.
- **Visual encoding:**
  - **Node size:** `contact_number`, or out-degree representing the number of people actually infected
  - **Node color:** `state`, such as isolated, released, or deceased
- **User insight:** A few large central nodes connected to dozens of smaller nodes make the effect of one-to-many transmission immediately visible.

##### Chart C: Transmission Sankey Diagram

**Objective:** Analyze which population groups were most affected by specific infection sources.

- **Chart type:** Sankey diagram
- **Data source:** `PatientInfo.csv`
- **Flow design:**
  - **Source, left:** Top five `infection_case` categories, such as Church, Overseas, and Hospital
  - **Target, right:** `age` or `sex`
- **Narrative:**
  - The flow from Shincheonji Church may primarily lead to women in their twenties, consistent with the younger demographic of many church members at the time.
  - Nursing Home flows may lead mainly to people in their eighties and to the deceased state, helping explain the higher fatality rate.

---

##### 3. AI Investigator

In this module, the AI acts as an **epidemiological investigator**.

- **Scenario:** The Sankey diagram shows that Itaewon Clubs primarily flows toward people in their twenties and toward men.
- **User question:** "Why was the Itaewon club transmission chain concentrated among young men?"
- **Backend logic:**
  1. **Retrieve:** Extract the Itaewon event's confirmed count and location, Yongsan-gu in Seoul, from `Case.csv`.
  2. **Context:** Calculate the average age of patients associated with the cluster in `PatientInfo.csv`, such as 25 years.
  3. **Prompt:** "The data shows that confirmed cases associated with the Itaewon club event were mainly men in their twenties. Analyze this pattern using social context—the characteristics of nightlife populations—and transmission factors such as enclosed spaces and limited mask use."
- **AI response:** The model explains how high density, poor ventilation, and removing masks to drink accelerated transmission among socially active young adults and may have led to secondary household transmission.

---

##### 4. Technical Considerations

1. **Handling missing `infected_by` values:**
   - Many `infected_by` values in `PatientInfo.csv` are `NaN`. Removing all such rows would leave the network graph sparse.
   - **Strategy:** Classify these nodes as **Unknown Origin**, or omit them only from the edge graph and display connected subgraphs with known transmission relationships.

2. **Linking `case_id` and `patient_id`:**
   - `case_id` in `Case.csv` has no direct foreign-key relationship with IDs in `PatientInfo.csv`.
   - **Join key:** The only shared key is the string field `infection_case`.
   - **Pitfall:** Strings may differ in capitalization or whitespace. Normalize both columns in `data_loader.py` with `str.strip().lower()` before joining.

3. **Network performance:**
   - Plotly becomes slow when rendering approximately 5,000 nodes.
   - **Optimization:** Display only the five largest clusters by default, or provide a dropdown for a specific transmission chain, such as the chain associated with Patient #31, South Korea's well-known super-spreader.

4. **Distinguishing geographic views:**
   - Module 2 maps where case volumes were high.
   - Module 3 can use coordinates from `Case.csv` to mark specific high-risk locations—churches and hospitals—with `Scattermapbox`, complementing the province-level view.

##### Example Pandas Preprocessing

```python
# Prepare Sankey data.
def get_sankey_data(df_patient):
    # Keep the top five infection sources and group the remainder as Others.
    top_cases = df_patient['infection_case'].value_counts().nlargest(5).index

    df_patient['source_label'] = df_patient['infection_case'].apply(
        lambda x: x if x in top_cases else 'Others'
    )

    # Aggregate Source -> Age.
    sankey_df = df_patient.groupby(['source_label', 'age']).size().reset_index(name='count')
    return sankey_df

# Prepare network data.
def get_network_data(df_patient):
    # Keep only records with an explicit transmission relationship.
    edges = df_patient.dropna(subset=['infected_by'])[['infected_by', 'patient_id']]
    # Convert IDs to strings to prevent formatting problems.
    edges['infected_by'] = edges['infected_by'].astype(str).str.replace('.0', '', regex=False)
    edges['patient_id'] = edges['patient_id'].astype(str)
    return edges
```

#### Module 4: Patient Profiles and Healthcare Resources

**Design concept:** Move from demographic characteristics to clinical outcomes. In addition to showing the age and gender distribution of confirmed cases, the module uses data to reveal differences in fatality risk and length of hospitalization across population groups.

##### 1. Data Mapping

This module combines complete aggregate statistics with individual case records.

- **`TimeAge.csv` and `TimeGender.csv` — complete time-series statistics:**
  - **Purpose:** Provide accurate cumulative counts of confirmed cases and deaths, as well as changes over time.
  - **Advantage:** Complete data without missing values, suitable for macro-level proportional analysis.
- **`PatientInfo.csv` — individual case data:**
  - **Purpose:** Calculate clinical duration, such as hospitalization days, and examine status distributions such as isolated, released, or deceased.
  - **Advantage:** Contains fields such as `confirmed_date`, `released_date`, and `state`, making it the only source for duration calculations.

---

##### 2. Core Visualization Components

##### Chart A: Demographic Pyramid

**Objective:** Compare the gender and age structure of confirmed patients.

- **Data source:** Latest-date records from `TimeAge.csv` and `TimeGender.csv`
- **X-axis:** Confirmed cases, with men on the left and women on the right
- **Y-axis:** Age groups from `0s` through `80s`
- **Design details:**
  - Use a back-to-back bar chart.
  - **Overlay:** Add darker death-count bars over confirmed-case bars.
  - **Insight:** People in their twenties may represent a large number of confirmed cases because of the Shincheonji Church outbreak, while people in their eighties have the largest share of deaths.

##### Chart B: Age Evolution Stacked Area Chart

**Objective:** Show how the distribution of infections shifted across age groups.

- **Data source:** `TimeAge.csv`
- **X-axis:** `date`
- **Y-axis:** `confirmed`, either as absolute values or percentage shares
- **Color:** Age groups from `0s` to `80s`
- **Narrative:**
  - **Early period, February–March:** The area for people in their twenties is very wide because the Daegu church outbreak affected many young female members.
  - **Later period, after April:** The share of people aged 60 and older gradually increases with community transmission and nursing-home outbreaks.
- **Interaction:** Hovering shows the dominant infected population at each point in time.

##### Chart C: Case Fatality Rate by Age

**Objective:** Quantify mortality risk.

- **Data source:** `TimeAge.csv`
- **Formula:** `Fatality Rate = (deceased / confirmed) * 100`
- **Chart type:** Gradient bar chart
- **X-axis:** `age`
- **Y-axis:** Fatality rate in percent
- **Visual encoding:** A gradient from green for low risk to dark red for high risk
- **Reference line:** Overall average fatality rate
- **Insight:** Clearly demonstrates the severity of COVID-19 among older adults, with the fatality rate for people in their eighties typically rising exponentially.

##### Chart D: Clinical Duration Boxplot

**Objective:** Analyze how long patients in different age groups remain hospitalized between confirmation and release, reflecting healthcare-resource utilization.

- **Data source:** `PatientInfo.csv`
- **Key preprocessing steps:**
  1. Filter to patients where `state == 'released'`.
  2. Calculate `df['duration'] = df['released_date'] - df['confirmed_date']`.
  3. Remove negative values and other anomalies.
- **Chart type:** Box-and-whisker plot
- **X-axis:** `age`
- **Y-axis:** `duration` in days
- **Insights:**
  - Younger patients may have shorter boxes, indicating faster recovery such as 14–20 days.
  - Older patients may have longer boxes and more outliers, suggesting more severe illness, longer bed occupancy, and greater pressure on healthcare resources.

---

##### 3. AI Medical Analyst

In this module, the AI acts as a **public-health expert**, explaining pathological and social factors behind the data.

- **Scenario:** The user clicks the `80s` bar in Chart C and sees a fatality rate above 20%.
- **Backend logic:**
  1. **Context:** Retrieve `confirmed` and `deceased` for people in their eighties.
  2. **External context:** Examine the association with `nursing_home_count` in `Region.csv`. Although this is regional data, it can be introduced as contextual information.
  3. **Prompt:**

     > "The data shows that South Korea's COVID-19 case fatality rate among people over 80 is much higher than in other age groups. Briefly explain why, considering immune-system characteristics and comorbidities among older adults. Regions with a higher `nursing_home_count` also tend to report more cases. What might this indicate?"

- **AI response:** Explain how immunosenescence makes viral clearance more difficult and how older adults often have conditions such as hypertension or diabetes. Also note that congregate nursing-home settings are an important social factor contributing to high mortality among older populations.

---

##### 4. Implementation Details

Calculating hospitalization duration with Pandas is one of this module's most error-prone steps because `PatientInfo.csv` contains many missing values.

```python
import pandas as pd

def calculate_duration(df_patient):
    # 1. Convert date columns.
    df_patient['confirmed_date'] = pd.to_datetime(df_patient['confirmed_date'], errors='coerce')
    df_patient['released_date'] = pd.to_datetime(df_patient['released_date'], errors='coerce')

    # 2. Keep released cases with valid confirmation and release dates.
    valid_cases = df_patient[
        (df_patient['state'] == 'released') &
        (df_patient['confirmed_date'].notnull()) &
        (df_patient['released_date'].notnull())
    ].copy()

    # 3. Convert the timedelta to an integer number of days.
    valid_cases['days_to_recover'] = (
        valid_cases['released_date'] - valid_cases['confirmed_date']
    ).dt.days

    # 4. Remove invalid negative durations and extreme values above 100 days.
    valid_cases = valid_cases[
        (valid_cases['days_to_recover'] >= 0) &
        (valid_cases['days_to_recover'] < 100)
    ]

    return valid_cases

# Use the result in the visualization function:
# fig = px.box(valid_cases, x="age", y="days_to_recover", color="sex", ...)
```

---

##### 5. Design Highlights

1. **Combines dynamic and static views:** `TimeAge` reveals trends over time, while `PatientInfo` reveals distributions at the individual level.
2. **Resource perspective:** Hospitalization duration goes beyond disease counts and provides an indirect measure of pressure on healthcare resources and bed turnover, which is important to government decision-makers.
3. **Clear analytical progression:** The module moves from simple gender proportions to deeper outcome analysis, serving users with different levels of expertise.

#### Module 5: Social and Environmental Impact

**Core logic:**

1. **Government intervention:** Did policy flatten the curve?
2. **Public attention:** Did fear spread faster than the virus?
3. **Social behavior:** Did people's movement reflect official guidance?
4. **Natural environment:** Did temperature affect viral activity?

---

##### 1. Data Preparation

This module aligns external explanatory data with the core outbreak timeline.

- **Core reference:** `Time.csv` for national daily new cases
- **Explanatory factors:**
  - `Policy.csv`: Policy types, alert levels, and start dates
  - `SearchTrend.csv`: Relative keyword search volumes from 0 to 100
  - `SeoulFloating.csv`: Seoul floating-population data requiring aggregation
  - `Weather.csv`: Temperature, humidity, and other weather measurements

---

##### 2. Core Visualization Components

##### Chart A: Policy Intervention Timeline

**Objective:** Show the temporal relationship between policy interventions and outbreak turning points. This is the key view for evaluating government effectiveness.

- **Data source:** `Time.csv` for the line or bars, plus `Policy.csv` for annotations
- **X-axis:** `date`
- **Y-axis:** `new_confirmed`
- **Visualization:**
  - **Base layer:** Gray bars for daily new confirmed cases
  - **Annotations:** Vertical dashed lines for policy announcement dates
  - **Interaction:** Because `Policy.csv` contains several policy categories, add a **Select Policy Type** filter:
    - `Alert`, from Blue to Red
    - `Immigration`, for entry restrictions
    - `Social Distancing`
    - `Education`, for school closures
- **Insight:** Users can see that daily cases began to fall substantially about two weeks—roughly one incubation period—after the Level 4 Red alert was issued on February 23.

##### Chart B: Digital Epidemiology — Search Trends versus the Outbreak

**Objective:** Test whether online searches can serve as a leading indicator.

- **Data source:** `SearchTrend.csv` and `Time.csv`
- **Chart type:** Dual-axis line chart
- **Left Y-axis:** Search volume
- **Right Y-axis:** Daily confirmed cases
- **Line design:**
  - `coronavirus`, solid red: fear or public attention
  - `cold` and `flu`, dashed blue: seasonal background noise
  - `new_confirmed`, gray filled area: the observed outbreak
- **Advanced lag analysis:** Add a **Shift Days** slider that moves the search curve 5–10 days to the right, letting users test whether peaks align.
- **Insight:** Search interest often rises sharply one or two weeks before a confirmed-case surge, then declines as the outbreak becomes normalized.

##### Chart C: Mobility and Social Distancing

**Objective:** Measure compliance with social-distancing policy. If official guidance changes but mobility does not, the policy may have had limited behavioral effect.

- **Data source:** `SeoulFloating.csv`
  - **Important:** The file contains a very large number of hourly records. It must be aggregated by day during loading with a process such as `groupby(['date', 'province']).sum()`.
- **Chart type:** Time-series area chart
- **X-axis:** `date`
- **Y-axis:** Total floating population
- **Reference context:** Mark weekends, when mobility is typically lower, and policy dates such as school closures in the chart background.
- **Insights:**
  - Did Seoul's floating population fall sharply after the Daegu church outbreak in late February?
  - Did mobility rebound after the Itaewon club event in early May?

##### Chart D: Weather Correlation Scatter Plot

**Objective:** Explore environmental factors. Although early research was disputed, this is a useful exploratory data analysis direction.

- **Data source:** `Weather.csv`, filtered to heavily affected provinces such as Daegu or Seoul, joined with `TimeProvince.csv`
- **Chart type:** Scatter plot with an OLS trendline
- **X-axis:** `avg_temp` or `avg_relative_humidity`
- **Y-axis:** `new_confirmed`
- **Color:** `month` to distinguish seasons
- **AI caution:** The analysis must state that correlation does not imply causation. Cases may fall as temperature rises because summer is approaching, because control measures have strengthened, or because of other confounding variables.

---

##### 3. AI Policy Analyst

In this module, the AI acts as a **sociologist and policy analyst**.

- **Scenario:** The user selects the period from `2020-02-20` to `2020-03-15` in Chart A.
- **Injected data context:**
  1. **Policy:** A Level 4 Alert was issued on February 23 and school opening was delayed on March 2.
  2. **Mobility:** Seoul's average floating population fell by 15%.
  3. **Cases:** Daily confirmed cases peaked at 909 on February 29, then declined.
- **Prompt construction:**

  > "The user is analyzing South Korea's outbreak from late February to mid-March 2020.
  >
  > The data shows that the government activated a Red alert on February 23 and delayed school opening on March 2.
  >
  > Behavioral data shows that Seoul's floating population fell by 15% during the same period.
  >
  > Confirmed cases peaked on February 29 and then declined.
  >
  > Analyze how government non-pharmaceutical interventions and changes in public mobility contributed to suppressing the outbreak peak."

- **AI response:** Using a retrieval-augmented reasoning chain, the model explains the sequence: Red alert -> reduced public movement -> interrupted transmission chains -> fewer confirmed cases.

---

##### 4. Technical Implementation

**Challenge 1: Preprocessing `SeoulFloating.csv`**

This is the largest CSV file in the project and must not be reread every time Streamlit refreshes.

```python
# Create a dedicated cached loader in utils/data_loader.py.
@st.cache_data
def load_mobility_data():
    # Prefer a preprocessed file containing only the date and total population.
    # Generate it during the first run if it does not exist.
    try:
        df = pd.read_csv('data/processed/SeoulFloating_Daily.csv')
    except FileNotFoundError:
        # Run this raw-data transformation only once.
        raw = pd.read_csv('data/raw/SeoulFloating.csv')
        df = raw.groupby('date')['fp_num'].sum().reset_index()
        df.to_csv('data/processed/SeoulFloating_Daily.csv', index=False)

    df['date'] = pd.to_datetime(df['date'])
    return df
```

**Challenge 2: Implementing a Dual-Axis Plotly Chart**

SearchTrend values range from 0 to 100, while case counts range from 0 to approximately 1,000, so the chart requires two Y-axes.

```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots

def plot_trend_vs_cases(df_search, df_time):
    # Create a figure with a secondary Y-axis.
    fig = make_subplots(specs=[[{"secondary_y": True}]])

    # Trace 1: search volume on the left axis.
    fig.add_trace(
        go.Scatter(
            x=df_search['date'],
            y=df_search['coronavirus'],
            name="Search: Coronavirus"
        ),
        secondary_y=False,
    )

    # Trace 2: confirmed cases on the right axis.
    fig.add_trace(
        go.Bar(
            x=df_time['date'],
            y=df_time['new_confirmed'],
            name="New Cases",
            opacity=0.3
        ),
        secondary_y=True,
    )

    fig.update_layout(title="Public Attention vs. Actual Outbreak")
    fig.update_yaxes(title_text="Relative Search Volume", secondary_y=False)
    fig.update_yaxes(title_text="Daily New Cases", secondary_y=True)
    return fig
```

---

##### 5. Design Highlights

1. **Introduces causal analysis:** The earlier modules describe what happened. Module 5 uses policy and mobility to examine why the outbreak was brought under control.
2. **Reduces high-dimensional data:** Millions of `SeoulFloating` records are transformed into an interpretable mobility index.
3. **Supports multidimensional attribution:** Digital footprints from search, physical footprints from mobility, and natural environmental conditions provide a comprehensive analytical perspective.
4. **Visualizes lag:** The dashboard helps non-specialist users understand why restrictions introduced today may not reduce confirmed cases until the following week, a central concept in public-health communication.
