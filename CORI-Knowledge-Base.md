# CORI Knowledge Base for Rural Economic Intelligence
**Version 1.0 | Last Updated: January 2025**

This knowledge base provides comprehensive information about rural economic development data, concepts, methodologies, and tools maintained by the Center on Rural Innovation (CORI). It is designed to support conversational AI interactions and data analysis guidance.

---

# 1. GEOGRAPHIC CONCEPTS & RURAL CLASSIFICATIONS

## Understanding Rural America: Geographic Frameworks

Geographic classification is fundamental to rural economic development analysis. Different federal agencies and research organizations use different systems to define "rural," each with specific purposes and strengths.

### Core Geographic Units

**County (5-digit GEOID)**
- The primary unit of analysis for most economic data in the United States
- Identified by Federal Information Processing Standard (FIPS) codes: 2-digit state + 3-digit county = 5-digit GEOID
- Examples: 01001 = Autauga County, Alabama; 06075 = San Francisco County, California
- Approximately 3,200 counties nationwide (includes county equivalents like Louisiana parishes, Alaska boroughs)
- Most federal economic statistics (BEA, BLS, Census) are published at county level
- Important: Store GEOIDs as text/strings to preserve leading zeros (e.g., "01001" not 1001)

**Census Tract (11-digit GEOID)**
- Smaller geographic unit within counties, typically 1,200-8,000 population
- Structure: state (2) + county (3) + tract (6) = 11 digits
- Approximately 80,000 census tracts nationwide
- Used for more granular analysis when county-level data is too coarse
- Tract boundaries follow visible features (roads, rivers) and political boundaries when possible
- Originally designed around 1910 to contain ~4,000 people; now more variable

**Census Block (15-digit GEOID)**
- Smallest census geographic unit; city block equivalent
- Structure: state (2) + county (3) + tract (6) + block (4) = 15 digits
- Over 11 million census blocks nationwide
- Used primarily for population/housing counts and broadband service location data
- Most economic data is NOT available at block level due to privacy concerns

**Place (7-digit GEOID)**
- Census-designated cities, towns, villages (incorporated or unincorporated)
- Structure: state (2) + place (5) = 7 digits
- Can span county boundaries (e.g., Kansas City spans Missouri and Kansas)
- Used for city-level analysis and program targeting (e.g., Build to Scale awards)
- Includes Census Designated Places (CDPs) for unincorporated communities

**ZIP Code and ZCTA (ZIP Code Tabulation Area)**
- ZIP codes are postal delivery routes, not geographic boundaries
- Created by U.S. Postal Service for mail delivery efficiency
- ZIP codes follow roads and are assigned to postal carriers
- At the smallest level, a ZIP code is tied to an address (point location)
- **Critical distinction**: ZIP codes ≠ geographic areas

**Why this matters**: Many users assume ZIP codes are stable geographic boundaries like counties or tracts. They're not. ZIP codes can:
- Change frequently as delivery routes change
- Cross county and even state boundaries
- Have no defined geographic boundaries (they're delivery routes)
- Vary in size from single buildings to entire regions

**ZIP Code Tabulation Areas (ZCTAs)**:
- Census Bureau's solution to make ZIP codes geographic
- Created by aggregating census blocks to approximate ZIP code coverage areas
- Structure: 5-digit code matching ZIP code (but stored as string to preserve leading zeros)
- Updated each decennial census (2010, 2020)
- Approximately 33,000 ZCTAs nationwide
- Use case: When you need to link ZIP-code referenced data (addresses, marketing data) to census geography

**ZCTA Limitations**:
- Not exact match to ZIP codes (approximations)
- Updated only every 10 years (ZIP codes change continuously)
- Some ZIP codes have no ZCTA (PO boxes, single buildings)
- Some ZCTAs don't match any current ZIP code

**Best practice**: Use county or tract for geographic analysis whenever possible. Only use ZCTA when source data is only available by ZIP code.

**Data source**: Census Bureau ZCTA relationship files for linking ZCTAs to counties/tracts
- https://www.census.gov/programs-surveys/geography/guidance/geo-areas/zctas.html

**Unincorporated Area**
- Definition: Geographic region NOT governed by a local municipal government
- Does not have its own city or town government (governed by county instead)
- Contrast with incorporated areas (cities, towns, villages with municipal governments)
- Common in rural areas and western states
- Residents rely on county government for services (law enforcement, planning, utilities)

**Census Designated Places (CDPs)**:
- Census Bureau's way of tracking population centers in unincorporated areas
- Example: "Cedar Hills, Oregon" is a CDP in Washington County (unincorporated)
- Allows Census to report statistics for named communities without municipal boundaries
- Updated each decennial census
- Important for rural community identification when no formal city boundaries exist

**Why this matters for rural development**:
- Many rural communities are unincorporated
- Affects eligibility for certain grants and programs (some require municipal government)
- Impacts service delivery (no city utilities, police, planning)
- Community identity may exist without legal boundaries

**Data source**: Census Bureau Place files include both incorporated places and CDPs
- https://www.census.gov/programs-surveys/bas/information/cdp.html

### Rural Classification Systems: Which One to Use?

**CRITICAL CONTEXT**: There is no single official definition of "rural." Seven major classification systems exist, each designed for different purposes. Choosing the right system depends on your analysis goals and data availability.

#### System 1: Core-Based Statistical Area (CBSA) - The Federal Standard

**When to use**: Official federal programs, recent data (2020+), county-level analysis

**How it works**:
- Developed by Office of Management and Budget (OMB)
- Based on urban cores and commuting patterns
- Updated every 10 years after decennial census

**Three categories**:
1. **Metropolitan** (Metro, M1): Urban core ≥50,000 population + adjacent counties with strong commuting ties
2. **Micropolitan** (Micro, M2): Urban cluster 10,000-49,999 population + adjacent counties
3. **Non-core** (Rural): Neither metro nor micro

**Rural definition**: Micropolitan + Non-core counties (or sometimes just Non-core depending on context)

**Advantages**:
- Official federal standard since 2020
- Widely recognized and used
- Clear metro vs. non-metro distinction

**Limitations**:
- Only available 2020+, cannot use for historical analysis pre-2020
- County-level only (too coarse for within-county variation)
- Binary classification may miss nuance

**Data availability**: CBSA 2020 delineations available from Census Bureau

#### System 2: CORI Rural Definition - Modern Hybrid Approach

**When to use**: Tract-level analysis, modern data (2020+), CORI projects

**How it works**:
- Developed by CORI combining CBSA metro status + RUCA codes
- Tract-level classification (more granular than county)

**Rural definition**: Tract is rural if EITHER:
1. RUCA code ≥ 4 (commuting-based rurality), OR
2. Located in micropolitan or non-core county (CBSA classification)

**Why it matters**: Captures rural tracts within metropolitan counties that other systems miss

**Advantages**:
- Most nuanced classification at tract level
- Combines commuting patterns + metro adjacency
- Identifies rural pockets within metro counties
- CORI's recommended standard for modern analysis

**Limitations**:
- Relatively new (2020 base year)
- Requires 2020 RUCA codes which are latest available
- Not yet widely adopted outside CORI

**Data availability**: ruraldefinitions R package from CORI GitHub

#### System 3: Rural-Urban Commuting Area (RUCA) Codes

**When to use**: Tract-level analysis, commuting pattern analysis

**How it works**:
- Developed by USDA ERS and WWAMI Rural Health Research Center
- Uses census tract population density, urbanization, and daily commuting flows
- Codes 1-10 representing urban core to isolated rural

**Rural definition**: RUCA code ≥ 4 (NOTE: use ≥ not >, common error)

**Categories**:
- Codes 1-3: Urban/metro
- Codes 4-6: Micropolitan
- Codes 7-10: Small town and rural

**Advantages**:
- Tract-level granularity
- Captures commuting relationships
- Nuanced 10-category scale

**Limitations**:
- Most recent data from 2010 census (14+ years old as of 2024)
- Has not been updated with 2020 census data
- Using outdated commuting patterns

**Data availability**: USDA ERS website, CORI uses 2010 version

#### System 4: NCHS Urban-Rural Classification

**When to use**: Health statistics, county-level analysis, disparity research

**How it works**:
- Developed by CDC National Center for Health Statistics
- Six categories from large central metro to non-core rural
- Based on CBSA status and metro size

**Six categories**:
1. Large central metro
2. Large fringe metro
3. Medium metro
4. Small metro
5. Micropolitan
6. Non-core (rural)

**Rural definition**: Codes 5-6 (Micropolitan + Non-core)

**Advantages**:
- Designed for health disparity analysis
- Updated each decennial census (1990, 2000, 2010, 2020)
- Six-level nuance useful for research
- Widely used in public health research

**Limitations**:
- County-level only
- Health-focused design may not fit all economic analysis

**Data availability**: CDC NCHS website for all census years

#### System 5: Rural-Urban Continuum Codes (RUCC)

**When to use**: Historical analysis (1974-2023), policy research, county-level

**How it works**:
- Developed by USDA Economic Research Service
- Nine categories distinguishing metro by size (1-3) and non-metro by urbanization and metro adjacency (4-9)

**Rural definition**: Codes 4-9 (all non-metro)

**Nine categories**:
- 1-3: Metro counties by population size
- 4-9: Non-metro counties by urbanization and metro adjacency

**Advantages**:
- 50+ years of data (1974-2023)
- Best for longitudinal historical analysis
- Distinguishes metro-adjacent vs. remote rural
- Widely used and understood

**Limitations**:
- County-level only
- Less granular than CBSA for recent years

**Data availability**: USDA ERS website, multiple vintage years

#### System 6: Urban Influence Codes (UIC)

**When to use**: Measuring metro influence, economic integration analysis

**How it works**:
- Developed by USDA ERS
- 12 categories based on metro adjacency and size of largest city
- Emphasizes economic integration with metros

**Rural definition**: Codes > 6 (WARNING: 2013 edition uses > 7)

**CRITICAL ALERT**: UIC 2013 has different thresholds
- 1974-2003: Rural when code > 6
- 2013: Rural when code > 7 (code 7 = URBAN in 2013 only!)
- 2024: Rural when code > 6 (back to original)

**Advantages**:
- Captures metro influence and economic integration
- Updated multiple times (1993, 2003, 2013, 2024)
- Useful for studying metro spillover effects

**Limitations**:
- Inconsistent threshold in 2013 (implementation trap!)
- County-level only
- Less commonly used than RUCC

**Data availability**: USDA ERS website

#### System 7: Census Urban/Rural Classification

**When to use**: Block-level analysis, most granular detail

**How it works**:
- Census Bureau's official designation at block level
- Based purely on population density and housing unit density

**Rural definition**: Blocks not in urbanized areas (≥50K pop) or urban clusters (2.5K-50K pop)

**Advantages**:
- Most granular (block-level)
- Updated each decennial census
- Official Census Bureau standard

**Limitations**:
- Block-level is often too granular for economic analysis
- Most economic data not available at block level
- Purely density-based (ignores economic relationships)

**Data availability**: Census TIGER files

### Choosing the Right Classification

**Decision Tree**:

1. **What geographic level do you need?**
   - Block → Use Census Urban/Rural
   - Tract → Use CORI (2020+) or RUCA (historical)
   - County → Choose based on time period and purpose

2. **What time period?**
   - 2020+ → CBSA (federal standard) or CORI (more granular)
   - 2010-2019 → RUCC or RUCA
   - Pre-2010 or long time series → RUCC (goes back to 1974)

3. **What's the purpose?**
   - Federal program eligibility → CBSA
   - Health statistics → NCHS
   - Economic integration study → UIC
   - General analysis → CBSA or RUCC
   - CORI project → CORI rural definition

**Most Common Use Cases**:
- County economic analysis recent data → **CBSA 2020**
- County historical trends → **RUCC**
- Tract-level modern analysis → **CORI 2020**
- Health disparities → **NCHS**

### Common Geography Questions

**Q: Why does my county show up as metro in one system and rural in another?**
A: Different systems have different criteria. A micropolitan county might be "rural" in some classifications but "not rural" in others depending on how micro areas are treated. Always specify which classification you're using.

**Q: Can I compare 2000 and 2020 data using the same rural definition?**
A: It depends on the system. RUCC and NCHS have versions for both years. CBSA 2000 and 2020 use different boundaries. You may need to use consistent RUCC vintages for true longitudinal comparison.

**Q: How do I handle metro-adjacent rural counties differently from remote rural?**
A: Use RUCC (codes 4-6 are metro-adjacent, 7-9 are not adjacent) or UIC which specifically measures metro influence.

**Q: What percentage of U.S. counties are rural?**
A: Approximately 60-65% depending on classification system. About 2,000 of 3,200 counties are non-metro under CBSA.

---

# 2. ECONOMIC DEVELOPMENT & WORKFORCE INDICATORS

## Understanding Economic Capacity and Performance

Economic development practitioners use dozens of indicators to assess regional economic health, capacity, and trends. CORI focuses on metrics particularly relevant to tech-based economic development in rural areas.

### Core Employment Metrics

**Total Employment**
- Definition: Count of all jobs (full-time + part-time) in a geography
- Source: Bureau of Economic Analysis (BEA) CAEMP25N table or CAINC1
- Includes: Wage and salary employees + proprietors (self-employed)
- Geographic levels: County, state, national
- Time series: 1969-present (CAINC1), 2001-2022 (CAEMP25N - being discontinued)
- Use cases: Economic size, growth trends, labor market capacity

**Employment-to-Population Ratio**
- Definition: Percentage of total population that is employed
- Formula: (Total employment / Total population) × 100
- Typical range: 35-55% (varies by age structure, retirement community, college town)
- Higher values indicate stronger labor force participation
- Lower values may indicate: aging population, college town, joblessness, or retirement destination
- Use case: Labor market participation compared to demographic composition

**Employment Growth Rate**
- Definition: Percentage change in employment over a period
- Formula: ((Employment_end - Employment_start) / Employment_start) × 100
- Common periods: 1-year, 5-year, 2020-2022 (pandemic recovery)
- Positive = job growth, Negative = job loss
- Use cases: Economic expansion/contraction, recovery assessment, trend identification

### Productivity & Compensation Metrics

**Real GDP per Worker**
- Definition: Inflation-adjusted Gross Domestic Product divided by total employment
- Units: Dollars per worker (typically chained 2017 dollars)
- Source: BEA CAGDP1 (GDP) ÷ CAEMP25N (employment)
- Typical range: $50K-$150K per worker (higher in capital-intensive industries)
- Measures: Economic productivity and output efficiency
- Higher values indicate higher-value economic activity (tech, finance, manufacturing)
- Lower values may indicate service-heavy or lower-wage economies
- Use case: Comparing economic productivity across regions

**Average Compensation per Job**
- Definition: Mean employee compensation including wages, salaries, and benefits
- Source: BEA CAINC6N table (Compensation of employees by industry)
- Includes: Direct wages + employer-provided health insurance + retirement contributions
- Typical range: $40K-$80K (varies widely by industry and region)
- Higher than wage data alone because includes benefits (~20-30% of total comp)
- Use case: Job quality assessment, wage level comparison

**How they relate**: High productivity often but not always correlates with high compensation. Capital-intensive industries (oil, mining) can have high GDP per worker with moderate compensation. Tech/finance typically high on both.

### Tech Economy Indicators

**Tech Employment Share**
- Definition: Percentage of workforce in computer and mathematical occupations
- Source: American Community Survey (ACS) table C24010
- SOC codes: 15-1000 series (software developers, data scientists, computer systems analysts, mathematicians)
- Typical values: 1-5% in most counties, 10-15% in tech hubs (Seattle, San Francisco, Austin)
- Rural typical: 0.5-2%
- Threshold for "tech presence": Generally >2% considered meaningful
- Use case: Assessing digital economy workforce capacity

**Why tech employment matters for rural communities**:
- Tech jobs are often high-wage, high-productivity
- Can be performed remotely, making rural location viable
- Indicates ecosystem capacity to support tech startups
- Correlates with broadband adoption and digital infrastructure

**Remote Work Prevalence**
- Definition: Percentage of workers who primarily work from home
- Source: ACS table B08006 (means of transportation to work - "worked from home")
- Pre-COVID typical: 5-10% in most areas
- Post-COVID (2021+): 15-30% in many areas, higher in tech-heavy regions
- Rural significance: Remote work enables access to metro-wage jobs without relocating
- Requires: High-quality broadband (fiber preferred)
- Use case: Assessing digital economy participation and broadband dependency

**Tradable Services Employment**
- Definition: Employment in service sectors that can be exported or consumed remotely
- Includes sectors:
  - Information (NAICS 51)
  - Finance and Insurance (NAICS 52)
  - Professional, Scientific, and Technical Services (NAICS 54)
- Source: BEA employment by industry tables
- Measured as: Share of total employment
- Significance: These are "economic base" activities bringing external revenue into region
- Contrast with: Local services (retail, accommodation, healthcare) serving local residents
- Use case: Identifying export-oriented economic activity

### Education & Human Capital

**Educational Attainment (Bachelor's Degree or Higher)**
- Definition: Percentage of population age 25+ with 4-year degree or higher
- Source: ACS table B15003
- National average: ~33% (2022)
- Metro typical: 30-40%
- Rural typical: 15-25%
- High-education rural counties: 25-35% (often college towns)
- Significance: Strong predictor of economic development capacity
- Correlates with: Tech employment, average wages, entrepreneurship
- Use case: Human capital assessment, workforce readiness

**Why education matters for tech ecosystems**:
- Tech companies require educated workforce
- College graduates more likely to start businesses
- Higher education institutions anchor tech ecosystems
- Indicates attraction/retention of talent

### Business Dynamics

**Establishment Growth Rate**
- Definition: Percentage change in number of business establishments
- Source: Census Business Dynamics Statistics (BDS)
- Formula: ((Establishments_end - Establishments_start) / Establishments_start) × 100
- Time series: 1978-present (2-year lag: 2022 data available Sept 2024)
- Typical values: -1% to +3% annual growth
- Significance: Measures entrepreneurial activity and business formation
- Can be segmented by: Firm age (young vs. mature), size (employees), industry
- Use case: Entrepreneurship assessment, economic vitality

**Business Applications (BFS)**
- Definition: Applications for Employer Identification Numbers (EINs) filed with IRS
- Source: Census Business Formation Statistics (BFS)
- What it captures: Intent to start a business (earlier indicator than BDS)
- Time series: 2005-present (6-month lag)
- Significance: Leading indicator before businesses open
- Limitations: Not all applications become operating businesses
- Use case: Entrepreneurial pipeline and startup formation trends

**Proprietor Employment Rate**
- Definition: Self-employed individuals and business owners as percentage of total employment
- Source: BEA CAEMP25N (line 60: Nonfarm proprietor employment)
- Typical values: 10-20% of employment
- Higher in rural areas often (agriculture, services, construction)
- Includes: Sole proprietors, partners in partnerships
- Significance: Proxy for entrepreneurship and small business activity
- Use case: Self-employment prevalence, entrepreneurial culture

### Common Analysis Questions

**Q: My county shows employment growth but population decline. How?**
A: This is common and can happen due to:
1. Increased labor force participation (more residents working)
2. In-commuting from other counties
3. Remote workers living elsewhere but jobs counted locally
4. Part-time jobs increasing while population shrinks

**Q: Why is Real GDP per worker higher than average compensation?**
A: GDP includes all economic output (profits, rents, taxes, depreciation), not just worker compensation. In capital-intensive industries, profits and returns to capital can be large.

**Q: How do I know if 3% tech employment is "good" for a rural county?**
A: Context matters:
- National average: ~3%
- Rural average: 1-2%
- 3% in rural context is above average and indicates meaningful tech presence
- Compare to peer counties, not metro tech hubs

**Q: What's a realistic target for broadband adoption in rural areas?**
A: Depends on availability and demographics:
- If fiber available: 70-85% adoption possible
- DSL/fixed wireless only: 50-70% typical
- Aging population: Lower adoption
- Young professionals with remote work: Higher adoption

---

# 3. ENTREPRENEURSHIP & VENTURE CAPITAL

## Understanding Startup Ecosystems and Capital Flows

Entrepreneurship is a key driver of economic growth and job creation. Rural areas face significant challenges in accessing venture capital and building startup ecosystems. Understanding these patterns is critical for targeted interventions.

### Venture Capital Landscape

**Venture Capital (VC) Basics**
- Definition: Private equity financing for high-growth-potential startups
- Typical stages:
  - Seed: <$2M, earliest stage
  - Early Stage (Series A): $2M-$15M
  - Late Stage (Series B+): $15M+
  - Growth/Pre-IPO: $50M+

**The Rural VC Gap**
- Geography matters: 70-80% of VC goes to 3 metro areas (SF Bay, NYC, Boston)
- Per capita disparity: Metro areas receive 10-100x more VC per capita than rural areas
- Coastal vs. interior: West Coast and Northeast dominate
- Rural VC challenges:
  - Distance from investors (VCs invest where they can visit easily)
  - Smaller networks (fewer connections to VC firms)
  - Perception bias (rural = lower tech capacity)
  - Lower deal flow (fewer startups in rural areas)

**Data Sources for VC Tracking**

1. **Crunchbase**
   - Commercial database of companies and investments
   - Self-reported data + curated by analysts
   - Coverage: Companies founded 2015+ in tech industries
   - Limitation: 1,000 company export cap (requires multiple queries for large states)
   - Best for: Tech-focused startups and funding rounds

2. **SEC Form D Filings**
   - Regulatory requirement for private securities offerings
   - Public data from U.S. Securities and Exchange Commission
   - Coverage: 2010-present
   - Includes: Issuer info, offering amounts, security types, investor counts
   - More comprehensive than Crunchbase (all industries)
   - Best for: Complete picture of private capital raises

**CORI integrates both sources**: Crunchbase for tech focus + Form D for comprehensive coverage

### Key VC Metrics

**Total Funding Amount**
- Definition: Cumulative capital raised by a company across all rounds
- Typical rural startup: $500K-$5M total
- Typical metro tech startup: $10M-$100M+ total
- Use case: Company growth stage, ecosystem capacity

**Number of Funding Rounds**
- Definition: Count of discrete capital raising events
- Typical pattern: Seed → Series A → Series B → etc.
- Multiple rounds indicate: Growing company, continued investor interest
- Rural challenge: Often struggle to raise beyond seed stage
- Use case: Ecosystem maturity assessment

**Venture Density**
- Definition: VC-backed companies per 1,000 residents or per square mile
- Source: GoDaddy Venture Forward data
- Metro typical: 2-5 ventures per 1,000 residents
- Rural typical: 0.1-0.5 per 1,000 residents (10-20x lower)
- Use case: Entrepreneurial ecosystem strength comparison

**What this means**: A rural county of 50,000 people might have 5-25 VC-backed companies total. A metro county of same size might have 100-250.

### Startup Identification

**CORI's Startup Definition** (Form D-based)
- Criteria: Founded year ≥ 2018 AND total capital raised > $0
- Why these criteria:
  - Founded ≥2018: Focus on recent/active companies
  - Capital raised >$0: Confirmed investor interest, not just intent
- Distinguishes from: All new businesses (many are small local services)
- Captures: Scalable, growth-oriented businesses

**Tech Startup**
- Additional criteria: In technology-related industries
- Crunchbase: 9 industry groups (Apps, AI, Data & Analytics, Gaming, Hardware, IT, Mobile, Platforms, Software)
- Form D: NAICS codes in computer/tech sectors
- Significance: Highest growth potential, most remote-work compatible

### Young Firm Dynamics

**Young Firm Employment**
- Definition: Jobs in businesses age 2-5 years
- Source: Census Business Dynamics Statistics (BDS)
- Why ages 2-5:
  - Year 1: Startup phase (volatile)
  - Years 2-5: Post-startup, entering growth phase
  - Years 6+: Established
- Significance: Measures ecosystem's ability to scale startups past initial phase
- Rural challenge: Many rural startups don't scale to significant employment

**Establishment Entry Rate**
- Definition: New establishments as percentage of total establishments
- Formula: (New establishments / Total establishments at period start) × 100
- Source: BDS
- Typical values: 10-15% annual entry rate
- High entry (>15%): Dynamic economy, entrepreneurial culture
- Low entry (<10%): Stagnant business environment
- Must consider exit rates: Net change = Entry - Exit

**Establishment Exit Rate**
- Definition: Closing establishments as percentage of total
- Typical values: 8-12% annual exit rate
- Exit > Entry = shrinking business base (concern)
- Exit < Entry = growing business base (healthy)

### Innovation Indicators

**Patent Counts**
- Definition: Number of utility patents assigned to entities in a geography
- Source: USPTO PatentsView database
- What counts: Utility patents (not design patents or trademarks)
- Location: Based on assignee address (company location, not inventor)
- Typical metro county: 50-500 patents per year
- Typical rural county: 0-10 patents per year
- Concentration: Heavily concentrated in metros with research institutions
- Use case: Measures innovation and inventive capacity

**Limitations of patent data**:
- Favors manufacturing and tech (doesn't capture service innovation)
- Lag time (2-3 years from application to grant)
- Location challenges (large companies may centralize patents at HQ)
- Rural undercount likely (innovation happens but not patented)

### Entrepreneurship Index

**What it is**:
- Composite score combining multiple entrepreneurial ecosystem indicators
- Typical components:
  1. Business formation rate
  2. Venture density
  3. Young firm employment
  4. Patent counts
  5. Self-employment rate

**How it's calculated**:
- Method: Principal Component Analysis (PCA) or weighted aggregation
- PCA extracts the common factor across all indicators
- Scores are standardized and often converted to percentiles/deciles
- Higher score = stronger entrepreneurial ecosystem

**CORI's Capital One Entrepreneurship Index**:
- Uses Robust PCA (Hubert's method) to handle outliers
- Combines 5 core indicators
- Presents as deciles (1=lowest 10%, 10=highest 10%) for accessibility
- Enables county comparison and identification of high-potential regions

### Common Questions

**Q: Why does our rural county have zero VC investment?**
A: This is the norm, not an exception. 80-90% of rural counties have little to no VC activity. VC is extremely geographically concentrated. Alternative capital sources (SBA loans, angel investors, community capital) are more relevant for most rural startups.

**Q: How can rural areas attract more VC?**
A: Realistic strategies:
- Focus on building strong local ecosystems (talent, infrastructure, entrepreneurial culture)
- Connect to regional VC through accelerators/pitch events
- Leverage remote work to recruit talent from metros
- Tell success stories (demonstration effect)
- Support angel investors and community capital funds
- Reality: Rural will never match metro VC levels; focus on right-sized capital

**Q: What's more important: patent counts or business formation?**
A: Depends on development strategy:
- Research-driven regions: Patents matter (university partnerships, R&D)
- Service/software regions: Business formation matters more (patents less common)
- For most rural areas: Business formation is more actionable indicator

**Q: How do I distinguish real startups from small businesses?**
A: Key differences:
- Startups: Scalable, growth-oriented, seeking external capital, tech/innovation focus
- Small businesses: Local market, organic growth, self/debt financed, traditional sectors
- In Form D data: Capital raise >$0 + tech industry = likely startup
- Both matter for rural economies, but different support needs

---

# 4. BROADBAND & DIGITAL INFRASTRUCTURE

## The Foundation of Digital Economy Participation

Broadband infrastructure is the fundamental enabler of digital economy participation. Without high-quality internet, rural communities cannot support remote work, tech employment, online businesses, or digital services. This section explains how broadband is measured, why it matters, and how to interpret broadband data.

### Understanding Broadband Metrics

**Critical Distinction: Availability vs. Adoption**

1. **Broadband Availability (Infrastructure)**
   - Definition: Whether broadband service CAN BE installed at a location
   - Source: FCC National Broadband Map
   - Question answered: "Is infrastructure present?"
   - Does NOT mean: Households actually have service

2. **Broadband Adoption (Subscription)**
   - Definition: Whether households actually subscribe to broadband service
   - Source: American Community Survey (ACS) table B28002
   - Question answered: "Are households using it?"
   - Requires: Both availability AND household decision to subscribe

**The Gap**: A county might have 90% availability but only 60% adoption. Reasons for gap:
- Affordability (monthly costs)
- Digital literacy
- Perceived need
- Age/demographics
- Alternative devices (mobile-only households)

### Broadband Speed Tiers

**Why speed matters**: Different activities require different speeds

**Minimum Threshold (25/3 Mbps)**
- 25 Mbps download / 3 Mbps upload
- Federal minimum definition of "broadband"
- Sufficient for: Basic web browsing, email, SD video streaming
- Not sufficient for: Multiple users, HD video conferencing, remote work, large file uploads

**Quality Threshold (100/20 Mbps)**
- 100 Mbps download / 20 Mbps upload
- Sufficient for: HD video conferencing, remote work, multiple users
- Standard for modern remote work
- Most rural areas struggle to reach this tier

**Premium Threshold (Fiber)**
- Typically 1000/1000 Mbps (1 Gigabit symmetrical)
- Sufficient for: Anything (future-proof)
- Technology: Fiber-optic cables (light-based, fastest available)
- Rural reality: Limited availability, expensive to deploy in low-density areas

### FCC National Broadband Map

**What it is**:
- Comprehensive database of broadband availability across U.S.
- Reports availability at individual location level (Broadband Serviceable Locations)
- Updated semi-annually (June and December releases)

**Key Data Elements**:

**Broadband Serviceable Location (BSL)**
- Definition: Business or residential location where broadband CAN BE installed
- Includes: Single-family homes, apartments, businesses
- Does NOT include: Temporary structures, vehicles
- Geographic representation: Census block + H3 hex index (NOT exact lat/long for privacy)

**Internet Service Provider (ISP)**
- Definition: Company that provides internet access service to customers
- Formally: FCC-registered provider offering mass-market broadband service
- Identified in FCC data by: FRN (FCC Registration Number), provider_id, and brand_name
- Each ISP can offer multiple technology types (fiber, cable, DSL, fixed wireless)
- Each ISP reports service availability by location and speed tier

**How ISPs appear in National Broadband Map**:
- A single location (BSL) can have service available from multiple ISPs
- Each ISP reports which technologies and speeds they offer at each location
- Example: A rural address might have:
  - ISP 1: DSL at 25/3 Mbps
  - ISP 2: Fixed wireless at 100/20 Mbps
  - ISP 3: Satellite at 100/10 Mbps

**Data quality considerations**:
- ISPs self-report their coverage (accuracy concerns exist)
- Some ISPs report if service "can be installed" vs. "is currently available"
- FCC challenges process allows disputing inaccurate claims
- Rural areas often have fewer ISP choices (less competition)

**Competition metrics**:
- Number of ISPs per location indicates competitive market
- Rural areas typically: 1-2 providers vs. metro areas: 3-5+ providers
- Lack of competition correlates with higher prices and lower service quality
- Monopoly markets (single ISP) common in remote rural areas

**Why this matters for rural development**:
- ISP infrastructure investment decisions determine rural broadband access
- BEAD program (Broadband Equity, Access, and Deployment) targets unserved/underserved locations
- Understanding which ISPs serve an area helps identify expansion opportunities
- Partnership opportunities with ISPs for community broadband projects

**Data source**: FCC National Broadband Map provider data
- https://broadbandmap.fcc.gov/data-download/nationwide-data

**Technology Types Tracked**:
- Fiber: Fiber-optic cables (best quality, highest speeds)
- Cable: Coaxial cable (good quality, moderate speeds)
- DSL: Telephone lines (lower speeds, degrades with distance)
- Fixed Wireless: Radio towers (moderate speeds, weather-dependent)
- Satellite: Space-based (high latency, improving with Starlink)
- Mobile: Cellular (variable, not ideal for home internet)

**Coverage Metrics**:
- Percent of locations with service at each speed tier
- Can aggregate to census block, county, state, national levels
- Most common metric: "% of locations with fiber available"

### Fiber Coverage - The Gold Standard

**Why fiber matters**:
- Fastest speeds (symmetrical gigabit common)
- Most reliable (not weather-dependent, doesn't degrade)
- Future-proof (capacity for growth)
- Supports multiple users and devices simultaneously
- Required for: Advanced remote work, telehealth, online education, tech entrepreneurship

**Fiber Coverage Metric**:
- Definition: Percentage of locations where fiber-optic service is available
- Formula: (Locations with fiber / Total locations) × 100
- National average: ~50% (2024)
- Metro typical: 60-80%
- Rural typical: 10-30%
- Rural challenge: Expensive to deploy in low-density areas (long distances, few customers)

**CORI tracks fiber coverage** in Rural Economic Development Tool and Calix Impact Tool as key infrastructure indicator.

### Broadband Adoption Analysis

**Adoption Rate**:
- Definition: Percent of households with ANY broadband subscription
- Source: ACS table B28002 (Presence and Types of Internet Subscriptions)
- Includes: Cable, fiber, DSL, satellite, mobile broadband
- Excludes: No internet, dial-up only
- National average: ~85% (2022)
- Rural typical: 70-80% (lower than metro)

**What drives adoption rates**:
1. Availability (can't adopt if not available)
2. Affordability (monthly cost barrier)
3. Digital literacy (understanding value/usage)
4. Demographics (older populations adopt less)
5. Alternative options (mobile-only households)
6. Perceived need (work-from-home drives adoption)

**Computer Access**:
- Related metric: Percentage of households with desktop or laptop computer
- Source: ACS table B28001
- Matters because: Smartphones alone insufficient for many activities (work, education)
- Rural challenge: Lower computer ownership correlates with lower broadband adoption

### Broadband Impact on Economic Outcomes

**The Calix Impact Tool Methodology**:

CORI's research (via Calix Impact Tool) quantifies broadband adoption's economic effects:

**Research Design**:
- Panel fixed effects regression models
- Outcome variables: Employment growth 2020-2022, Establishment growth 2020-2022
- Key predictor: Broadband adoption rate (% of households subscribed)
- Controls: Median age, distance to metro, education, poverty, demographics, population, QoL, lagged growth

**Key Findings**:
- Broadband adoption positively associated with employment growth
- Effect is stronger in rural counties than metros
- If low-performing rural counties increased broadband adoption from 20th percentile to current level, they would have experienced X% higher employment growth
- Establishment growth also positively correlated with broadband

**Counterfactual Analysis**:
- Estimates "what would have happened" if broadband adoption had been lower
- Example: County with 80% adoption vs. counterfactual 60% adoption
- Difference in employment/establishments = "impact" of broadband investment

**Practical implication**: Broadband infrastructure investments can be justified using estimated job/business impacts.

### Common Questions

**Q: Our county shows 95% broadband availability but only 65% adoption. Why the gap?**
A: Common reasons:
- Affordability: Monthly costs ($50-$100) are barrier
- Age: Older residents may not see need
- Mobile-only: Some rely solely on smartphones
- Awareness: Don't know service is available
- Solution: Focus on digital inclusion programs, affordability assistance (ACP/Lifeline), digital literacy training

**Q: Is satellite internet like Starlink equivalent to fiber?**
A: Not quite:
- Speed: Can match fiber download speeds
- Latency: Higher latency (delay) than fiber (noticeable in video calls, gaming)
- Reliability: Weather-dependent
- Cost: Often more expensive
- Capacity: Can slow during peak usage
- Role: Good gap-filler where fiber not available, but fiber preferable when possible

**Q: How much broadband adoption is "enough" for digital economy?**
A: Targets depend on goals:
- Basic connectivity: 70-80% adoption
- Remote work economy: 80-85% adoption
- Tech hub aspiration: 85%+ adoption
- Reality: Unlikely to reach 100% (choice, affordability, demographics)

**Q: Should we prioritize broadband availability or adoption?**
A: Both required, sequence matters:
1. First: Build availability (infrastructure)
2. Then: Drive adoption (digital inclusion, affordability, literacy)
3. Finally: Utilize for economic development (remote work, entrepreneurship)

**Q: How do I calculate economic return on broadband investment?**
A: Use impact analysis approach:
1. Estimate adoption increase from infrastructure investment
2. Apply impact coefficients from regression models (employment/establishment effects)
3. Translate to job counts: Jobs gained = (adoption increase) × (impact coefficient) × (current employment)
4. Monetize: Jobs gained × average wage = annual economic impact

---

# 5. HOUSING & QUALITY OF LIFE

## Measuring Community Attractiveness and Livability

Quality of life (QoL) is a critical but difficult-to-measure concept. It encompasses all the factors that make a place desirable to live beyond just employment and wages. CORI uses hedonic pricing methodology to extract location-specific QoL from housing market data.

### The Hedonic Pricing Framework

**Core Principle**:
People "vote with their feet" (and wallets). If a place is desirable, housing prices will be higher than income alone would predict. The unexplained difference represents quality of life.

**The Logic**:
1. Housing prices reflect both household income (ability to pay) and location desirability
2. After controlling for income, remaining price variation reflects location-specific factors
3. These factors include: Climate, natural amenities, safety, schools, culture, pollution, density

**Basic Model**:
```
ln(housing_price) = β₀ + β₁ × ln(income) + error

QoL = error (residual)
```

**Enhanced Model** (CORI's Detailed QoL):
```
ln(housing_price) = β₀ + β₁ × ln(income)
                  + β₂ × housing_characteristics
                  + β₃ × demographics
                  + β₄ × amenities
                  + error

QoL = error (residual after controlling for everything)
```

### CORI's QoL Measurement

**Data Sources**:
- Housing prices: American Community Survey (ACS) median home values
- Income: ACS median household income
- Housing characteristics: ACS housing tables (bedrooms, age, vacancy)
- Demographics: ACS (education, children, household composition)
- Amenities: USDA Natural Amenities Scale

**Time Coverage**: 1980-2023
- Census snapshots: 1980, 1990, 2000, 2010
- ACS periods: 2015, 2020, 2023
- Interpolation: Linear interpolation for non-census years, weighted by House Price Index (HPI) volatility

**Two QoL Measures**:

1. **Base QoL**
   - Simple model: price ~ income only
   - Captures all non-income factors
   - Easier to interpret but less precise

2. **Detailed QoL**
   - Enhanced model with controls
   - Isolates pure location effects after removing observable characteristics
   - More precise but harder to interpret
   - CORI's preferred measure

**Interpretation**:
- Positive QoL: Housing prices higher than income predicts (desirable location)
- Negative QoL: Housing prices lower than income predicts (less desirable)
- Standardized scores: Converted to standard deviations from mean
- Categorized: -3 to +3 scale for accessibility

### Natural Amenities - What Makes Places Desirable

**USDA Natural Amenities Scale**:
A composite index of physical/environmental features that enhance location appeal

**Six Dimensions**:
1. **Climate**: Warm winters, temperate summers (sunshine, low humidity)
2. **Topography**: Hills, mountains (varied terrain preferred over flat)
3. **Water**: Lakes, rivers, coastline (water access highly valued)
4. **Forest**: Tree cover (green landscapes preferred)
5. **Parks**: National/state parks and recreation areas
6. **Low elevation**: Coastal/low elevation slightly preferred

**Scale**: 1-8 (1 = lowest amenities, 8 = highest)

**High Amenity Regions** (7-8):
- Coastal California
- Mountain West (Colorado, Montana)
- Pacific Northwest
- Upper Midwest lake regions
- Parts of Appalachia

**Low Amenity Regions** (1-2):
- Great Plains
- Desert Southwest (hot summers)
- Parts of Deep South (hot, humid)

**Research finding**: Natural amenities explain 39-43% of housing price variation

### Housing Affordability Metrics

**Cost-Burdened Households**:
- Definition: Spending ≥30% of income on housing (rent or mortgage)
- Source: ACS table B25106
- Measured separately for renters and owners
- National average: ~25-30% of households cost-burdened
- Rural typical: 20-25% (lower housing costs but also lower incomes)
- Metro typical: 30-40% (higher costs)

**Why 30% matters**: HUD standard for "affordable" housing
- <30% = Affordable
- 30-50% = Cost-burdened
- >50% = Severely cost-burdened

**Implications**:
- High burden = affordability crisis, economic stress
- Low burden = affordable housing, but may indicate weak economy
- Balance needed: Want affordable without economic stagnation

**Housing Supply Metrics**:

**Vacancy Rate**:
- Definition: Percentage of housing units unoccupied
- Formula: (Vacant units / Total units) × 100
- Typical healthy market: 5-10% vacancy
- High vacancy (>15%): Population loss, declining demand, economic distress
- Low vacancy (<3%): Tight market, housing shortage, upward price pressure

**Building Permits**:
- Source: Census Building Permits Survey (BPS)
- Tracks: Applications for new residential construction
- Categories: 1-unit, 2-unit, 3-4 unit, 5+ unit structures
- Measured: Units authorized, units started, valuation
- Indicates: Housing market dynamics, growth expectations
- Rural challenge: Lower permit activity (limited growth, population decline)

**CORI normalizes to**: Permits per 1,000 residents for comparability

### Housing Stock Characteristics

**Housing Unit Age**:
- Cohorts: 10-year, 20-year, 30-year, 40-year, 45+ years old
- Source: ACS year-built distributions
- Significance: Newer stock generally valued higher, indicates investment/growth
- Old stock: May indicate disinvestment, maintenance challenges, or historic character
- Use: Control variable in QoL models

**Bedroom Count**:
- Common metric: Percentage with 3+ bedrooms
- Indicates: Family-sized housing, home sizes
- Rural typical: Higher % large homes (land availability, lower costs)
- Urban typical: More small units (space constraints, higher costs)

### Interpreting QoL Scores

**High QoL Locations** (positive residuals, +1 to +3 SD):
- Examples: Mountain resort towns (Jackson WY, Aspen CO), coastal areas (Santa Barbara CA), college towns (Boulder CO)
- Characteristics: High amenities, desirable lifestyle, strong in-migration
- Housing expensive relative to wages
- Challenge: Affordability for workers

**Low QoL Locations** (negative residuals, -1 to -3 SD):
- Examples: Declining industrial towns, isolated rural areas, areas with environmental issues
- Characteristics: Out-migration, population loss, limited amenities
- Housing cheap relative to wages
- Opportunity: Affordability attracts some (retirees, remote workers)

**Average QoL** (near 0):
- Most counties cluster here
- Housing prices match income expectations
- Neither premium nor discount for location

### Common Questions

**Q: Our county has low QoL scores but residents love it. Is the measure wrong?**
A: Not necessarily. QoL measures revealed preference (housing prices), not stated preference (surveys). Residents may love community for reasons not reflected in housing market (family ties, culture, not valued by new residents). Or sample size small (residents not moving, no new buyers bidding up prices).

**Q: Can low QoL be improved?**
A: Yes, but slowly:
- Amenity investments: Parks, trails, downtown revitalization, cultural venues
- Economic development: Job growth attracts new residents
- Perception: Marketing and storytelling
- Infrastructure: Broadband, healthcare, schools
- Reality: Natural amenities hard to change (can't add mountains), economic fundamentals matter most

**Q: Why do some rural counties have high QoL and low economic outcomes?**
A: "Amenity migration" pattern:
- Retirees/remote workers attracted by scenery, recreation, lifestyle
- Bid up housing prices
- But limited employment opportunities for locals
- Creates bifurcated economy: wealthy newcomers + struggling longtime residents
- Example: Many Rocky Mountain counties

**Q: How does QoL relate to economic development?**
A: Complex relationship:
- High QoL can attract talent, entrepreneurs → economic growth
- But expensive housing can deter workers, businesses
- Low QoL may repel talent → economic decline
- But affordability can attract cost-sensitive businesses, remote workers
- Sweet spot: Moderate QoL + affordable housing + economic opportunity

---

# 6. DATA SOURCES & FEDERAL STATISTICS

## Navigating the Federal Data Ecosystem

Understanding where data comes from, how it's collected, and what it represents is critical for correct interpretation and analysis. This section provides practical guidance on major data sources used in rural economic development research.

### Bureau of Economic Analysis (BEA)

**Authority**: U.S. Department of Commerce agency producing economic statistics

**What BEA Provides**:
- Gross Domestic Product (national, state, county)
- Personal income (national, state, county)
- Employment by industry (national, state, county)
- Compensation (wages + benefits)
- Regional price parities

**Key BEA Tables for County Analysis**:

1. **CAEMP25N** - Total Employment by Industry
   - Full-time + part-time employment counts
   - By NAICS industry sector
   - 2001-2022 (being discontinued after 2022!)
   - Use for: Employment by sector, location quotients, industry analysis

2. **CAINC1** - Personal Income Summary
   - Population counts
   - Per capita personal income
   - Total personal income
   - 1969-present
   - Use for: Population trends, income levels, economic size

3. **CAINC6N** - Compensation of Employees
   - Wages, salaries, and benefits by industry
   - 2001-present
   - Use for: Average compensation, job quality, wage analysis

4. **CAGDP1** - GDP Summary
   - County-level GDP by industry
   - Real (inflation-adjusted) and current dollars
   - 2001-present
   - Use for: Productivity (GDP per worker), economic output

**Update Cycles**:
- Personal income: November (prior year data)
- GDP: December (prior year data)
- Employment: Variable (being discontinued)
- Lag: 6-12 months typical

**Critical Note**: CAEMP25N discontinued after 2022
- No future employment-by-industry updates planned
- Must find alternative sources for post-2022 data
- Options: County Business Patterns (CBP), ACS occupation data, private sources (Lightcast)

**Data Access**:
- Website: https://www.bea.gov
- API: Available with free key
- R packages: bea.R (official), coriverse has wrappers

### Census Bureau Programs

**American Community Survey (ACS)**

**What it is**:
- Ongoing survey replacing long-form decennial census
- Samples ~3.5 million households annually
- Produces 1-year and 5-year estimates

**When to use which**:
- **5-year estimates**: Counties <65,000 population (most rural)
  - Larger sample, more reliable
  - But: 5-year average (midpoint year reported)
  - Example: "2018-2022 5-year" represents ~2020 conditions
- **1-year estimates**: Counties >65,000 population only
  - Specific year data
  - But: Smaller sample, less reliable for small populations

**Major ACS Data Categories**:
- Demographics: Age, sex, race, ethnicity (B01001, B02001, B03002)
- Education: Educational attainment (B15003)
- Employment: Labor force, unemployment, occupation (B23001, C24010)
- Income: Household income, poverty (B19001, B19013, B19083)
- Housing: Tenure, costs, values, age (B25001-B25127, DP04)
- Commuting: Means of transportation, travel time (B08006, B08303)
- Internet: Computer access, broadband subscriptions (B28001, B28002)

**Table Naming**:
- B tables: Detailed data
- C tables: Collapsed categories
- DP tables: Data profiles (pre-aggregated)
- S tables: Subject tables (broader topics)

**Margins of Error**:
- All ACS estimates have margins of error (MOE)
- Smaller geographies = larger MOEs
- Must consider when comparing or testing differences
- 90% confidence intervals reported

**Access**:
- Website: data.census.gov
- API: Free with key
- R package: tidycensus (highly recommended)

**Business Dynamics Statistics (BDS)**

**What it measures**:
- Establishment births, deaths, expansions, contractions
- Employment creation and destruction
- By firm age, size, industry

**Time Coverage**: 1978-present
- Annual data
- 2-year publication lag (2022 data → Sept 2024 release)

**Geographic Levels**: Nation, state, metro, county

**Critical Feature**: Historical Redistribution
- When county boundaries change, BDS redistributes ALL historical data to new boundaries
- Example: Connecticut reorganized from 8 counties to 9 planning regions in 2022
- BDS rewrote history: All years now reflect new boundaries
- Implication: May lose historical comparability if boundaries changed

**CORI's Solution**: Archive pre-change data (bds_tidy_2010_2021 table for CT/AK)

**Key Metrics**:
- Establishment count (total active businesses)
- Firm count (distinct companies)
- Job creation (new jobs)
- Job destruction (lost jobs)
- Net job creation (creation - destruction)
- Entry/exit rates

**Use Cases**: Entrepreneurship, business dynamics, economic churn

**Business Formation Statistics (BFS)**

**What it measures**: Business applications (EIN filings with IRS)

**Why it matters**: Leading indicator before businesses open

**Time Coverage**: 2005-present, 6-month lag

**Frequency**: Monthly and annual

**Geographic Levels**: National, state, county

**What's Included**:
- All EIN applications
- Excludes: Tax liens, estates, trusts, certain ag/gov sectors

**What's NOT Included**: Whether business actually opened (just application)

**Interpretation**: High applications indicate entrepreneurial intent and pipeline

**Building Permits Survey (BPS)**

**What it measures**: Applications for new residential construction

**Categories**:
- 1-unit structures (single-family homes)
- 2-unit structures (duplexes)
- 3-4 unit structures (small multifamily)
- 5+ unit structures (large multifamily/apartments)

**Data Points**:
- Units authorized (permits issued)
- Valuation (estimated construction value)

**Time Coverage**: 1990-present, annual

**Update Cycle**: May of following year (2024 data → May 2025)

**CORI Normalization**: Permits per 1,000 residents (comparability across county sizes)

### FCC National Broadband Map

**Purpose**: Track broadband availability at individual location level

**Coverage**: All U.S. addresses (Broadband Serviceable Locations)

**Update Cycle**: Semi-annual (June and December releases)

**Technology Types**: Fiber, cable, DSL, fixed wireless, satellite, mobile

**Speed Tiers**: Reports availability at multiple speed thresholds (25/3, 100/20, 1000/1000)

**Geographic Representation**: Census block + H3 hex (not exact coordinates)

**Data Format**: Large files (block-level = millions of records)

**Aggregation**: CORI aggregates to county/state for usability

**Limitations**:
- Self-reported by ISPs (accuracy concerns)
- "Availability" means CAN install, not IS installed
- May overstate coverage in sparse areas
- No pricing information

### Private/Academic Sources

**PatentsView (USPTO)**
- Utility patent data with enhanced location coding
- 1976-present
- Assignee and inventor locations
- Used for: Innovation indicators

**GoDaddy Venture Forward**
- Venture density metrics
- Microbusiness data
- County-level aggregations
- Used for: Entrepreneurship indicators

**Crunchbase**
- Commercial startup/VC database
- 2015-present focus
- Tech industry heavy
- Used for: VC activity tracking

**IPUMS NHGIS**
- Historical census data with consistent boundaries
- 1790-present
- Used for: Long-term demographic trends

### Data Quality Considerations

**Missing Data Patterns**:
- Small counties: Higher suppression rates (privacy)
- Rural areas: Less data collection infrastructure
- New businesses: Lag before appearing in BDS/BEA

**Margin of Error**:
- Always check MOE in ACS data
- Overlapping MOEs = differences may not be significant
- Small geographies = large MOEs

**Lag Times**:
- Real-time: Stock market (not used)
- 1-3 months: BLS monthly employment
- 6 months: BFS business applications
- 1 year: ACS, most BEA tables
- 2 years: BDS, Census economic data

**Boundary Changes**:
- County mergers/splits rare but impactful
- Census tract boundaries change each decade
- Always verify consistent geographic definitions

### Common Data Questions

**Q: Why do BEA and ACS show different population numbers?**
A: Different sources and timing:
- BEA: Uses Census population estimates (July 1 reference date)
- ACS: Survey-based sample (annual average)
- Small differences normal (<1%), large differences indicate data issue

**Q: Can I combine 2010 and 2020 tract data directly?**
A: No, tract boundaries changed. Options:
1. Use county-level data (stable boundaries)
2. Use crosswalks to harmonize tract boundaries
3. Accept incomparability and note in analysis

**Q: Why is my county missing from BDS?**
A: Likely suppression due to:
- Too few establishments (disclosure risk)
- Dominated by single large employer
- Recent boundary change
- Not missing - it's data protection

**Q: Which is better: ACS 1-year or 5-year?**
A: Depends on need:
- Need current snapshot + large population → 1-year
- Need reliability + small population → 5-year
- Most rural analysis → 5-year (only option for small counties)

### CORI Data Infrastructure Terminology

These terms describe how CORI organizes, stores, and manages data internally. Understanding this terminology helps when working with CORI's data systems and understanding data lineage.

**Data Archive**
- Definition: Remote storage system for immutable (unchangeable) source data artifacts
- Purpose: Long-term preservation of original data as received from authoritative sources
- Characteristics:
  - Immutable: Once stored, never modified (ensures data provenance)
  - Versioned: Each data release stored separately with timestamps
  - Documented: Metadata includes source, date acquired, version, processing notes
  - Indexed: Searchable catalog of all archived datasets

**What gets archived**:
- Original source files from federal agencies (BEA, Census, FCC downloads)
- Raw data before any CORI processing or cleaning
- Historical snapshots of data at specific points in time
- Product outputs from completed analysis projects

**Why this matters**:
- Reproducibility: Can recreate analysis from original sources
- Data lineage: Track where data came from and when
- Recovery: Restore data if working copies corrupted
- Compliance: Meet data management and documentation requirements
- Historical comparison: Access previous versions of datasets

**Technology**: Typically cloud object storage (AWS S3, Azure Blob Storage, Google Cloud Storage)

**Example use case**:
- CORI downloads BEA CAEMP25N employment data in December 2023
- Archive stores: `bea-caemp25n-2023-12-15-raw.csv` (never modified)
- Later processing creates cleaned versions, but original always available
- If methodology changes, can reprocess from archived source

**Data Cache**
- Definition: Remote storage system for mutable (changeable) representations or views of data products
- Purpose: Fast access to frequently-used, processed data for applications and tools
- Characteristics:
  - Mutable: Can be updated, refreshed, or replaced as needed
  - Performance-optimized: Structured for quick access (JSON, indexed databases)
  - Application-specific: Formatted for frontend tools, APIs, dashboards
  - Temporary: Can be regenerated from archive + processing scripts

**What gets cached**:
- JSON files for Rural Economic Development Tool (county-specific data files)
- Pre-aggregated summary statistics for dashboard display
- Frequently-queried database tables
- Processed datasets ready for analysis or visualization

**Why this matters**:
- Performance: Applications load data quickly (no processing wait time)
- Scalability: Multiple users access same cached data (no redundant computation)
- Separation of concerns: Frontend doesn't need access to raw data or processing code
- Update cycle: Cache refreshed periodically (monthly, quarterly) as new data arrives

**Technology**:
- Cloud object storage (AWS S3 for JSON files)
- Redis or Memcached for frequently-accessed queries
- Content delivery networks (CDN) for distributed access

**Example use case**:
- Rural Economic Development Tool caches pre-computed JSON files in S3
- When user visits dashboard for Adams County, WA (GEOID 53001):
  - Frontend fetches `53001.json` from S3 cache (fast!)
  - No database query, no computation needed
  - If new BEA data releases, pipeline regenerates all county JSON files
  - Old cache replaced with updated version

**Archive vs. Cache - Key Differences**:

| Aspect | Data Archive | Data Cache |
|--------|--------------|------------|
| **Mutability** | Immutable (never changed) | Mutable (updated regularly) |
| **Purpose** | Preservation, lineage | Performance, access |
| **Content** | Original source data | Processed, formatted data |
| **Lifespan** | Permanent (historical record) | Temporary (regenerable) |
| **Structure** | As received from source | Optimized for application |
| **Updates** | New versions added | Existing content replaced |
| **Users** | Data engineers, researchers | Applications, end users |

**CORI's Data Flow**:
```
Source (BEA, Census, FCC)
  ↓ Download
Data Archive (S3: immutable storage)
  ↓ Load & Process (R scripts)
PostgreSQL Database (proj_erc, proj_calix_impact_tool schemas)
  ↓ Assemble & Format (R scripts)
Data Cache (S3: JSON files, tidy tables)
  ↓ Frontend Fetch
User-Facing Tools (ERC Dashboard, Calix Impact Tool)
```

**Best practices**:
- Always archive raw source data before processing
- Document archive with metadata (source URL, date, version)
- Cache can be deleted/regenerated; archive cannot
- Use version control for processing scripts (links archive → cache)
- Monitor cache freshness (flag stale data)

---

# 7. ANALYTICAL METHODS & METHODOLOGIES

## How to Analyze Economic Data Correctly

Understanding analytical methods is crucial for correct interpretation and avoiding common errors. This section covers key techniques used in rural economic development analysis.

### Data Structures

**Tidy Data Format**
- Standard: Each row = one observation, each column = one variable
- CORI standard: (geoid, year, variable, value, agg_var)
- Benefits: Consistent, joinable, analysis-ready
- All CORI source tables use `_tidy` suffix

**Panel Data**
- Definition: Multiple observations of same units over time
- Structure: Geographic unit × time periods
- Example: All U.S. counties observed 2017-2022
- Enables: Time series + cross-sectional analysis
- Two formats:
  - Long: One row per geography-year-variable
  - Wide: Year as columns, one row per geography

**Square-Up Operation**
- Purpose: Create complete geographic × year matrix
- Method: Outer joins to ensure every geography represented every year
- Handles: Missing data explicitly (NA) rather than omitting
- Why needed: Visualization tools expect complete grids
- CORI implementation: `square_up_data()` function in assembly pipelines

### Regression Analysis

**Linear Regression Basics**
- Purpose: Model relationship between outcome (Y) and predictors (X)
- Form: Y = β₀ + β₁X₁ + β₂X₂ + ... + error
- Coefficients (β): Estimated effect of one unit change in X on Y
- R-squared: Proportion of variance explained (0-1 scale)

**Interpreting Coefficients**:
- β₁ = 0.5 means: 1-unit increase in X → 0.5-unit increase in Y
- Sign matters: Positive = positive relationship, Negative = inverse
- Significance: p-value <0.05 typically considered statistically significant

**Log Transformations**
- Common in economic analysis: ln(Y) = β₀ + β₁ln(X) + error
- Why log: Handles skewed data, interprets as percentage changes
- Interpretation: β₁ = elasticity (% change in Y per 1% change in X)
- Example: ln(price) ~ ln(income), β₁=0.8 means 1% income increase → 0.8% price increase

**Fixed Effects Models**
- Purpose: Control for unobserved time-invariant characteristics
- Use case: Panel data with repeated observations
- How: Includes entity-specific intercepts (one per geography)
- Effect: Removes confounding from unchanging factors
- CORI use: Calix Impact Tool broadband adoption effects

**Why fixed effects matter**:
- Counties differ in unmeasured ways (culture, history, institutions)
- Fixed effects remove these differences
- Estimates only within-county changes over time
- More credible for causal inference

**Residual Analysis**
- Residual: Observed value - predicted value
- Purpose: Identify patterns not captured by model
- QoL application: Residuals = location effects after controlling for income/characteristics
- Use: Diagnostic tool (check model fit) or measure itself (QoL scores)

### Principal Component Analysis (PCA)

**Purpose**: Combine multiple correlated variables into fewer uncorrelated components

**Use Case**: Creating composite indices (Entrepreneurship Index, Distress Index)

**How it works**:
1. Standardize all variables (mean=0, SD=1)
2. Find linear combinations that explain maximum variance
3. First component (PC1) explains most variance
4. Extract PC1 scores as composite index

**Robust PCA** (CORI's approach):
- Uses Hubert's method to reduce outlier sensitivity
- Important for county data (many extreme values)
- More stable indices

**Advantages**:
- Data-driven weighting (not arbitrary)
- Uncorrelated components
- Captures common factor across indicators

**Interpretation**:
- Higher scores = higher on combined dimension
- Converted to percentiles/deciles for accessibility
- Loadings show which variables contribute most

### Counterfactual Analysis

**Purpose**: Estimate "what would have happened" under alternative scenario

**Calix Impact Tool Example**:
1. Estimate relationship: Employment growth = f(broadband adoption, controls)
2. Predict outcome under counterfactual: "What if adoption was only 20th percentile?"
3. Compare: Actual outcome - counterfactual outcome = impact
4. Convert: Percentage point difference × current employment = jobs gained/lost

**Key Requirements**:
- Valid model (controls for confounders)
- Reasonable counterfactual (not too extreme)
- Stable relationships (coefficients apply to counterfactual range)

**Interpretation**:
- Impact = effect attributable to treatment (e.g., broadband)
- Causal language requires strong assumptions
- Better phrased: "associated with" or "consistent with"

### Propensity Score Matching

**Purpose**: Create comparable control groups in observational data

**Use Case**: B2S program evaluation (compare winners to similar non-winners)

**How it works**:
1. Estimate probability of treatment (receiving B2S award) based on pre-treatment characteristics
2. Match treated units to similar untreated units based on propensity score
3. Compare outcomes between matched pairs
4. Difference = treatment effect estimate

**Advantages**:
- Reduces selection bias
- Makes observational data more like randomized experiment
- Transparent matching process

**Limitations**:
- Requires rich pre-treatment data
- Can only control for observed characteristics
- Unobserved confounders still problematic

### Aggregation Methods

**Why aggregation matters**: County data needs to aggregate correctly to state/national

**Three Methods CORI Uses**:

1. **Sum**
   - Use for: Counts (employment, population, establishments)
   - State total = sum of county values
   - Example: Alabama employment = sum of all AL county employment

2. **Weighted Average**
   - Use for: Rates, percentages, per capita measures
   - Weight: Population or employment
   - Formula: Σ(county_rate × county_weight) / Σ(county_weight)
   - Example: State broadband adoption = weighted average of county rates by households

3. **Mean (Unweighted)**
   - Use for: Indices, scores where each geography counts equally
   - State average = mean of county values
   - Example: Average QoL percentile (treating counties as equals)

**Stored in Metadata**: `agg_var` field stores weight for weighted aggregation

### Interpolation & Temporal Methods

**Linear Interpolation**
- Purpose: Estimate values between known data points
- Common use: Fill gaps between census years (1985 estimated from 1980 and 1990)
- Method: Straight line between points
- Assumes: Linear trend (may not hold)

**Weighted Interpolation**
- Enhancement: Use external indicator (e.g., House Price Index volatility)
- Allocates change proportionally to indicator fluctuations
- More accurate than linear for volatile series
- CORI use: QoL time series weighted by HPI

**Growth Rates**
- Formula: ((Value_end - Value_start) / Value_start) × 100
- Units: Percentage
- Interpretation: 5% = 5% increase, -3% = 3% decrease
- Annualized: For multi-year periods, can calculate compound annual growth rate (CAGR)

### Data Quality & Validation

**Outlier Detection**
- Methods: IQR (interquartile range), Z-score (standard deviations), visual inspection
- CORI uses: Multiple methods flagged in index tables
- Decision: Flag vs. exclude (usually flag for transparency)

**Missing Data Handling**
- Patterns: Random missing vs. systematic (small counties suppressed)
- Approaches:
  - Complete case: Drop observations with any missing
  - Imputation: Estimate missing values (use carefully)
  - Explicit NA: Keep structure, mark missing
- CORI preference: Explicit NA with missing flags

**Data Validation Checks**
- Range checks: Values within plausible bounds
- Consistency: Same value across sources agrees
- Trend checks: Implausible jumps investigated
- Duplicate detection: Same geography-year-variable unique

### Common Analysis Pitfalls

**Ecological Fallacy**
- Error: Inferring individual behavior from aggregate data
- Example: "County has high education and high income, therefore educated people earn more"
- Problem: Correlation at county level doesn't mean individual causation
- Solution: Be cautious with language, use individual-level data when possible

**Simpson's Paradox**
- Error: Trend reverses when data disaggregated
- Example: Overall trend positive, but negative within each subgroup
- Cause: Compositional changes (e.g., fast-growing rural counties might have low wage growth, but county mix shifting)
- Solution: Stratify analysis, examine subgroups

**Correlation ≠ Causation**
- Classic error: Assuming correlation implies causal relationship
- Example: "Counties with more broadband have more employment growth, therefore broadband causes growth"
- Problem: Could be reverse causation, confounding, or coincidence
- Solution: Use causal inference methods (fixed effects, matching, instrumental variables)

**Selection Bias**
- Error: Sample not representative of population
- Example: B2S award recipients are self-selected (applied and won)
- Problem: Winners different from non-applicants in unmeasured ways
- Solution: Propensity score matching, control variables, acknowledge limitations

---

# 8. CORI TOOLS & APPLICATIONS

## How CORI's Data Infrastructure Works

CORI maintains several interactive tools and data products for rural economic development analysis. Understanding how these tools work and what data they contain enables effective use and troubleshooting.

### Rural Economic Development Tool (ERC)

**What it is**:
- Interactive web-based dashboard for exploring county-level economic indicators
- Publicly accessible at https://rural-economic-development-dashboard.ruralinnovation.us/
- Powers data analysis for Economic Recovery Corps (IEDC partnership) fellows and general public

**Data Coverage**:
- 12 data sources integrated: ACS, BEA, BDS, BFS, BPS, FCC, Form D, PatentsView, Pitchbook, FHFA, BLS, QoL
- 31 primary metrics across 5 categories
- Geographic coverage: All 3,200+ U.S. counties, 50 states, national aggregates
- Time range: Varies by source (typically 2017-2023, some historical data)

**Five Metric Categories**:

1. **Workforce** (8 metrics)
   - Employment trends
   - Tech employment share
   - Educational attainment
   - Average compensation
   - Real GDP per worker
   - Prime-age employment
   - Remote work prevalence
   - Tradable services employment

2. **Entrepreneurship** (13 metrics)
   - Establishment counts and growth
   - Business applications (BFS)
   - Venture capital activity (Form D)
   - Patent counts
   - Startup company counts
   - Self-employment rates
   - Industry concentration (HHI)
   - Young firm employment

3. **Infrastructure** (3 metrics)
   - Broadband availability (25/3, 100/20, fiber)
   - Broadband adoption/subscription
   - Computer access

4. **Demographics** (6 metrics)
   - Population trends
   - Median age
   - Race/ethnicity distributions
   - Income distributions
   - Gini inequality index
   - Poverty rates

5. **Quality of Life** (1 metric)
   - QoL percentile ranking (county comparison)

**How Users Interact**:
1. Select primary county (or multiple counties for comparison)
2. Choose comparison geographies: State, US national, peer counties, rurality groups
3. View charts: Line charts (trends), bar charts (comparisons), stat cards (key figures)
4. Download graphics (PNG) for reports, presentations
5. Access data interpretations ("How to interpret" and "Why it matters" guidance)

**Technical Architecture**:
- Frontend: React/TypeScript web application
- Data delivery: JSON files stored in AWS S3
- Data structure: One JSON per county (e.g., 01001.json for Autauga County AL)
- Metadata: Separate metadata.json with chart specifications
- Backend: PostgreSQL database (proj_erc schema) for data staging

**Data Pipeline**:
```
Raw sources → Load scripts (R) → PostgreSQL (proj_erc schema)
→ Assembly script → Tidy data (erc_data_tidy table)
→ JSON generation → AWS S3
→ Frontend fetches on demand
```

**Metadata-Driven Design**:
- Metrics Codebook (Google Sheets): Defines chart types, titles, subtitles, formatting
- Variables Codebook: Maps variables to metrics, provides labels
- Research Language: "How to interpret" and "Why it matters" text
- Changes to metadata automatically propagate to tool (no code changes needed)

### Calix Impact Tool

**Purpose**: Measure and visualize broadband adoption's economic impact on rural communities

**Unique Features**:
- Econometric modeling (fixed effects regression)
- Counterfactual impact estimates (jobs/businesses gained due to broadband)
- Benchmark comparisons (rural/non-rural × high/low performing counties)

**Data Coverage**:
- 8 data sources: ACS, BEA, BDS, BFS, FCC, QoL, distance to metro, natural amenities
- 14 primary metrics focused on economic outcomes
- Impact statistics: Estimated employment and establishment counts attributable to broadband
- Time range: 2017-2023 (with 2020-2022 pandemic recovery focus)

**Impact Analysis Methodology**:
1. Regression model: Economic growth = f(broadband adoption, controls)
2. Separate models for rural vs. non-rural counties
3. Estimate counterfactual: "What if adoption was only at 20th percentile?"
4. Calculate difference: Actual - counterfactual = impact
5. Convert to counts: Jobs gained = % growth difference × current employment

**Benchmark Groups**:
- Rural low performers (<20th percentile broadband adoption)
- Rural high performers (>80th percentile)
- Non-rural low performers
- Non-rural high performers

**Use Cases**:
- Justify broadband infrastructure investments (ROI calculation)
- Target communities for digital inclusion programs
- Compare county performance to peers
- Generate impact talking points for grants/advocacy

**Technical Architecture**:
- Backend: PostgreSQL (proj_calix_impact_tool schema)
- Analysis: R scripts with plm package (panel models)
- Data delivery: JSON files to AWS S3 (calix-impact-tool bucket)
- Separate impact statistics JSONs per county

### Rural Insights Hub (Planned Agentic AI)

**Vision**: Conversational AI system for exploring CORI's curated economic data and institutional knowledge

**Status**: In development, planned pilot with Rural Innovation Network (RIN) communities

**Key Capabilities**:
1. **Natural Language Queries**: "Should we open an incubator in our county?"
2. **Guided Exploration**: Agent suggests relevant data, alternative visualizations
3. **Insight Pinning**: Users save key findings during exploration
4. **Report Generation**: Agent creates branded reports from pinned insights
5. **Contextual Guidance**: Explains "why this matters" and interpretation

**Technical Architecture**:
- LLM: AWS Bedrock with Claude
- Knowledge Base: Vector database (OpenSearch) with CORI reports, blog posts, DEER assessments
- Data Access: Same databases as ERC tool (PostgreSQL)
- Orchestration: Agentic logic framework with specialized agents
- Interface: Conversational chat UI

**Knowledge Sources**:
- CORI research reports
- Blog articles
- Digital Economy Ecosystem Reports (DEER)
- Community assessments
- Best practices documentation
- THIS ontology and knowledge base

**Agentic Logic**:
- User query → Intent classification → Data retrieval → Analysis → Synthesis → Response
- Multi-step reasoning (plan, retrieve, analyze, synthesize)
- Cites data sources (transparency)
- Generates visualizations (React components from tool)
- Handles follow-up questions (maintains context)

**Pilot Strategy**:
- Phase 1: RIN communities only (closed beta)
- Phase 2: Expand to ERC fellows and CORI staff
- Phase 3: Public access (conditional on success)

**Guardrails** (Critical for Accuracy):
1. Grounding in curated data (not web search)
2. Required citations (all claims must reference sources)
3. Data availability pre-check (don't hallucinate data)
4. Confidence scoring (flag uncertain responses)
5. User feedback loop (thumbs up/down)
6. Human review for high-stakes decisions

### Data Infrastructure

**PostgreSQL Schemas**:
- `proj_erc`: Rural Economic Development Tool data
- `proj_calix_impact_tool`: Calix Impact Tool data
- `venture_capital`: VC and startup data
- `proj_qol`: Quality of Life data
- `sch_broadband`: FCC broadband data
- `location_analysis`: Supplementary economic data

**Tidy Table Structure** (All Sources):
```
Columns: geoid, year, variable, value, agg_var
- geoid: 5-digit county FIPS or 2-digit state FIPS
- year: Observation year
- variable: Metric name (snake_case)
- value: Numeric value
- agg_var: Weight for aggregation (population, employment, or NULL)
```

**Assembly Process** (Consistent Across Tools):
1. Load raw data from APIs/sources
2. Transform to tidy format
3. Write to database (_tidy tables)
4. Square-up (complete geoid × year matrix)
5. Join all sources
6. Add metadata (labels, metrics)
7. Generate outputs (JSON, CSV, database tables)

**coriverse & cori.db Packages**:
- Internal R packages for data operations
- Database connection management
- Common data transformation functions
- Ensures consistency across projects

**AWS S3 Storage**:
- Staging: dev/, test/, prod/ prefixes
- Bucket structure: {bucket}/{stage}/s3/{files}
- Deployment: R function pushes local files to S3
- Frontend: Fetches on-demand from S3

### Tool Usage Guidance

**When to Use ERC Tool**:
- General county economic exploration
- Comparison to state/national/peers
- Recent data (2017-2023)
- Downloadable graphics needed
- Public use, no login required

**When to Use Calix Tool**:
- Broadband impact quantification
- Justifying infrastructure investment
- Identifying low-performing counties for targeting
- Benchmark comparisons

**When to Use Rural Insights Hub** (Future):
- Question-answering (vs. exploration)
- Report generation from conversation
- Guided analysis (not sure what to look for)
- Learning about rural economy concepts
- Interpreting data in context

**Common Tool Questions**:

**Q: Why does county X show missing data in the tool?**
A: Possible reasons:
- Privacy suppression (too few establishments)
- Data not collected at county level
- County merged/split (boundary changes)
- Technical issue (data pipeline problem)
- Check source documentation for coverage

**Q: Can I download all data at once?**
A: Not directly from web interface, but:
- JSON files are publicly accessible in S3
- Contact CORI for database access
- Use tool's "Download" feature per chart

**Q: How often is data updated?**
A: Varies by source:
- ACS: Annual (December release)
- BEA: Annual (various months)
- BDS: Annual (September, 2-year lag)
- FCC: Semi-annual (June and December)
- Tool: Updated quarterly or when new data available

**Q: Why do comparison groups change over time?**
A: Benchmark group membership based on current year broadband adoption percentiles. Counties can move between groups as adoption changes.

---

# 9. RURAL ECONOMIC DEVELOPMENT PRACTICE

## Applying Data to Real-World Challenges

Understanding data is one thing; using it effectively for economic development is another. This section provides practical guidance for applying CORI's data and tools to common rural development scenarios.

### Common Rural Development Scenarios

**Scenario 1: Assessing Tech Ecosystem Readiness**

**Question**: "Is our county ready to support a tech incubator or accelerator?"

**Data to examine**:
1. **Tech employment share**: >2% indicates existing tech workforce
2. **Educational attainment**: >20% bachelor's+ suggests talent base
3. **Broadband adoption**: >75% needed for remote work/online businesses
4. **Fiber coverage**: >50% ideal for future-proofing
5. **Remote work prevalence**: >15% shows digital economy participation
6. **Business formation rate**: Above state average suggests entrepreneurial culture
7. **Young firm employment**: Indicates capacity to scale startups

**Red flags**:
- Tech employment <1% (no existing workforce to draw from)
- Broadband adoption <60% (infrastructure barrier)
- Bachelor's degrees <15% (limited talent base)
- Negative population trend (out-migration of talent)

**Green lights**:
- Tech employment >2%
- Growing remote work
- Presence of higher education institution within 30-minute drive
- Positive establishment growth
- High broadband adoption

**Recommendation framework**:
- 5-7 green lights: Strong candidate, move forward
- 3-4 green lights: Moderate candidate, focus on gaps first
- 0-2 green lights: Weak candidate, fundamental capacity building needed first

**Scenario 2: Justifying Broadband Investment**

**Question**: "How do we justify $20M broadband infrastructure investment?"

**Data approach**:
1. Current broadband adoption rate (e.g., 65%)
2. Target adoption with new infrastructure (e.g., 80%)
3. Impact coefficient from Calix Tool (e.g., 0.045 = 4.5% employment growth per 10% adoption increase)
4. Calculation:
   - Adoption increase: 80% - 65% = 15 percentage points = 0.15
   - Employment effect: 0.15 × 0.045 = 0.00675 = 0.675% growth
   - Current employment: 15,000 jobs
   - Jobs impact: 15,000 × 0.00675 = 101 jobs
   - Annual wages: 101 jobs × $45,000 average = $4.5M/year
   - 5-year impact: $22.5M
   - ROI: $22.5M / $20M = 1.13 (positive return)

**Additional benefits to quantify**:
- Property value increases
- New business formation (establishments impact)
- Improved quality of life
- Educational outcomes
- Healthcare access (telehealth)

**Scenario 3: Identifying Peer Communities**

**Question**: "Which counties should we compare ourselves to?"

**Comparison strategies**:

1. **Same rurality classification**
   - Use CBSA or RUCC to find similarly rural counties
   - Ensures comparable context

2. **Similar economic structure**
   - Compare location quotients (industry specializations)
   - Find counties with similar employment mix

3. **Similar demographics**
   - Match on population size (±25%)
   - Match on median age (±5 years)
   - Match on education levels (±10 percentage points)

4. **Similar broadband/infrastructure**
   - Match on fiber coverage
   - Match on adoption rates

5. **Aspirational peers**
   - Similar size/structure but better outcomes
   - Identify what they do differently

**Tool approach**:
- ERC Tool: Select multiple counties for side-by-side comparison
- Create 2x2 matrix: Rural/non-rural × High/low performing
- Identify counties in same quadrant

**Scenario 4: Building Economic Development Strategy**

**Question**: "What should our 5-year economic development strategy prioritize?"

**Data-driven prioritization process**:

1. **Identify Strengths**
   - What metrics are above average for rurality classification?
   - What location quotients >1.5 (specializations)?
   - What growth trends positive?

2. **Identify Gaps**
   - What metrics below average?
   - What infrastructure deficits?
   - What workforce capacity limitations?

3. **Assess Opportunities**
   - What adjacent industries (location quotient 0.8-1.2, growing)?
   - What remote work trends emerging?
   - What higher education assets nearby?

4. **Prioritize Actions**
   - Build on strengths (sector specializations)
   - Address critical gaps (broadband if <70% adoption)
   - Seize opportunities (grow adjacent industries)

**Strategy framework**:
- Short-term (1-2 years): Infrastructure, capacity building
- Mid-term (3-4 years): Talent attraction, business support
- Long-term (5+ years): Ecosystem development, sectoral growth

### Using Data for Grant Applications

**Effective Data Presentation**:

1. **Establish Need**
   - Show gaps: "Our county's broadband adoption is 62%, compared to 78% statewide"
   - Use comparisons: Rural vs. metro, county vs. state vs. national
   - Visualize: Use ERC Tool graphics (downloadable)

2. **Quantify Impact**
   - Project outcomes: "Expected to create 50 jobs over 3 years"
   - Base on models: Cite Calix Tool impact estimates
   - Show multipliers: Direct + indirect effects

3. **Demonstrate Feasibility**
   - Show assets: "We have 3 higher ed institutions within 30 minutes"
   - Show momentum: "Business formation increased 12% last 5 years"
   - Show readiness: "Broadband infrastructure 80% complete, need last-mile"

4. **Provide Context**
   - Explain trends: Not just snapshot, show trajectory
   - Account for rural context: Compare to peers, not metros
   - Note data limitations: "Data understates due to suppression"

**Common Grant Data Requirements**:
- Distress indicators (unemployment, poverty, population loss)
- Demographics (population, age, race/ethnicity)
- Economic structure (employment by industry, wages)
- Infrastructure (broadband, transportation)
- Innovation capacity (patents, startups, higher ed)

**CORI's ERC Tool as Evidence**:
- Professional-quality graphics (no Excel charts!)
- Cited sources (federal agencies)
- Rural-specific comparisons
- Up-to-date data (refreshed annually)

### Communicating Data to Stakeholders

**Principle 1: Know Your Audience**
- **Elected officials**: Focus on jobs, population, investment
- **Businesses**: Focus on workforce, infrastructure, customers
- **Community members**: Focus on quality of life, opportunity, services
- **Funders**: Focus on outcomes, impact, leverage

**Principle 2: Tell Stories with Data**
- Don't: "Our location quotient in manufacturing is 1.4"
- Do: "Manufacturing is 40% more concentrated here than typical county, showing regional strength"
- Don't: "Broadband adoption increased from 62% to 68%"
- Do: "850 more households now connected to high-speed internet, enabling remote work"

**Principle 3: Use Visuals**
- Line charts for trends
- Bar charts for comparisons
- Maps for geographic patterns
- Icons/infographics for key stats

**Principle 4: Provide Context**
- Always compare (to state, nation, peers)
- Show trajectories (not just current status)
- Explain "why this matters"
- Acknowledge limitations

**Principle 5: Be Honest**
- Don't cherry-pick only positive data
- Acknowledge challenges
- Frame gaps as opportunities
- Show you understand the full picture

### Common Development Questions

**Q: Our county lost population but gained jobs. Is that good?**
A: Mixed signal, investigate further:
- Good: Economic growth attracting in-commuters
- Concerning: Workforce outmigration (living elsewhere, working here)
- Check: Remote workers relocating out but keeping jobs
- Consider: Demographic shifts (aging, out-migration of families)
- Action: Focus on attraction/retention of residents, not just jobs

**Q: We want to be the next Austin/Boulder/Seattle. Realistic?**
A: Very unlikely, better to be realistic:
- Those cities built over decades (not years)
- Had major anchors (universities, tech companies, natural amenities)
- Benefited from timing and network effects
- Better goal: "Best version of ourselves" not "next Austin"
- Focus: Build on unique assets and comparative advantages

**Q: Should we recruit a big employer or support entrepreneurs?**
A: Both/and, not either/or:
- Recruitment: Faster job creation, higher risk (can leave)
- Entrepreneurship: Slower growth, more resilient (local ownership)
- Best: Balanced portfolio approach
- Reality: Big employer recruitment very competitive for rural areas
- Pragmatic: Support existing businesses and entrepreneurs, opportunistic recruitment

**Q: How long does economic development take?**
A: Longer than politics, but:
- Infrastructure (broadband): 1-3 years to deploy
- Workforce development: 2-5 years (education cycles)
- Business growth: 3-7 years (startups to scale)
- Ecosystem development: 10+ years (culture change)
- Implication: Need sustained commitment across political cycles
- Strategy: Mix quick wins (1-2 years) with long-term investments

---

# 10. USING THIS KNOWLEDGE BASE

## How the Agentic AI System Should Use This Information

This knowledge base is designed for conversational AI retrieval and question-answering. Here's guidance for effective use:

### Query Understanding & Intent Classification

**When a user asks a question, determine:**

1. **What type of question is it?**
   - Definitional: "What is CBSA?"
   - Comparison: "How does rural compare to metro?"
   - Analytical: "Should we invest in broadband?"
   - Data location: "Where can I find employment data?"
   - Interpretation: "What does this number mean?"
   - Methodological: "How is this calculated?"

2. **What concepts are involved?**
   - Identify geographic terms (county, tract, rural, metro)
   - Identify economic terms (employment, GDP, entrepreneurship)
   - Identify data sources (ACS, BEA, BDS)
   - Identify methods (regression, PCA, interpolation)

3. **What context is needed?**
   - Rural vs. urban
   - Time period (historical vs. current)
   - Geographic level (block, tract, county, state)
   - Data availability constraints

### Retrieval Strategy

**For definitional questions**:
- Retrieve concept definitions
- Include related terms (cross-references)
- Provide examples
- Note common confusions

**For comparison questions**:
- Retrieve definitions of both concepts
- Explain differences explicitly
- Provide decision criteria
- Include use case guidance

**For analytical questions**:
- Retrieve relevant scenario guidance
- Include required data points
- Provide interpretation framework
- Reference specific tools

**For data questions**:
- Retrieve data source information
- Include access methods
- Note data limitations
- Provide update schedules

### Response Construction

**Always include**:
1. Direct answer to question
2. Necessary context
3. Relevant examples
4. Important caveats or limitations
5. Related concepts user might want to know
6. Specific data sources cited

**Example Good Response Structure**:
```
[Direct answer]
[Explanation with context]
[Relevant example]
[Important caveat]
[Related information user might need]
[Data sources cited]
```

**Example**:
User: "How do I know if my county is rural?"

Response:
"Your county's rural classification depends on which system you're using, as there's no single federal definition of 'rural.'

The most common current standard is the Core-Based Statistical Area (CBSA) system from the Office of Management and Budget. Under CBSA:
- Metropolitan counties (≥50,000 urban core) = Not rural
- Micropolitan counties (10,000-49,999 urban core) = Sometimes rural
- Non-core counties = Rural

For example, a county like Madison County, Montana (population ~8,500) is classified as non-core rural, while a county like Missoula County, Montana (population ~120,000) is metropolitan (not rural).

Important caveat: The rural classification can differ depending on which system you use (CBSA vs. RUCC vs. RUCA). If you're applying for federal programs, check which classification system that program requires.

To look up your specific county, you can use the CBSA 2020 delineations from the Census Bureau, or CORI's ruraldefinitions R package for automated classification.

[Sources: OMB CBSA delineations, Census Bureau geography]"

### Handling Uncertainty

**When data is not available or question is ambiguous**:

1. **Acknowledge uncertainty explicitly**: "I don't have data for that specific question"
2. **Offer alternatives**: "However, I can provide related information about..."
3. **Explain why**: "This data is not collected at that geographic level"
4. **Suggest workarounds**: "You might be able to use X as a proxy"
5. **Don't hallucinate**: Never make up data or facts

### Context Maintenance

**Track conversation context**:
- User's geographic focus (county, state, region)
- Time period of interest
- Analysis goal (grant application, strategy, research)
- Technical level (beginner, intermediate, expert)

**Adapt responses accordingly**:
- Beginner: More explanation, simpler language, more examples
- Expert: Technical depth, methodological details, limitations
- Grant application: Focus on justification, impact, comparisons
- Strategy: Focus on implications, decisions, actions

### Proactive Guidance

**When appropriate, proactively offer**:
- Related concepts user might need
- Common follow-up questions
- Methodological considerations
- Data limitations user should know
- Alternative approaches to consider

**Example**:
User asks about broadband adoption rates.
Proactive addition: "Note that adoption (households subscribing) is different from availability (infrastructure present). Many counties have high availability but lower adoption due to affordability or digital literacy. Would you like information on either specifically?"

### Error Prevention

**Common user errors to catch**:
- Confusing availability vs. adoption
- Using wrong rural classification for context
- Comparing incompatible time periods
- Ignoring margins of error in ACS data
- Assuming correlation implies causation
- Treating metro benchmarks as rural targets

**When detected, gently correct**:
"I notice you're comparing your rural county to San Francisco. While that's possible, you might get more actionable insights comparing to peer rural counties or your state average. Would you like to see those comparisons instead?"

### Tool Integration

**When user questions could be answered by tools**:
- Reference specific tools: "You can explore this interactively in CORI's Rural Economic Development Tool at [URL]"
- Explain what tool shows: "The tool will show your county compared to state and national averages"
- Guide navigation: "Select your county, then look at the 'Workforce' category for tech employment data"

**When to recommend tool vs. answer directly**:
- Simple factual questions: Answer directly
- Exploration/comparison needs: Recommend tool
- Multiple metrics: Recommend tool
- Visual needs: Recommend tool (downloadable graphics)

### Citation & Transparency

**Always cite sources**:
- Data sources: "According to Census Bureau's American Community Survey..."
- Methodologies: "Based on CORI's hedonic pricing analysis..."
- Limitations: "Note that this data has a 2-year lag..."
- Uncertainty: "This estimate has a margin of error of ±500"

**Format for citations**:
[Source: Organization Name, Dataset/Table, Year]
Example: [Source: Bureau of Economic Analysis, CAEMP25N, 2022]

### Learning from Interactions

**Track patterns** (for system improvement):
- Common questions (expand knowledge base coverage)
- Confusing concepts (improve explanations)
- Data gaps (prioritize new data integration)
- User goals (tailor responses better)

### Final Principles

1. **Accuracy over completeness**: Better to say "I don't know" than guess
2. **Context over facts**: Explain why it matters, not just what it is
3. **Guidance over prescription**: Help users think through decisions, don't make decisions for them
4. **Transparency over opacity**: Explain reasoning, cite sources, acknowledge limitations
5. **Rural-first perspective**: Always consider rural context, don't default to metro assumptions

---

**END OF KNOWLEDGE BASE**

This knowledge base will be continuously updated as new data, tools, and insights emerge. Version 1.0 represents the comprehensive documentation of CORI's rural economic development data infrastructure as of January 2025.

For questions about this knowledge base, contact CORI's Data Analytics team.
