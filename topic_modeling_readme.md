# Agentic Topic Modeling System with LangGraph

## Overview

An intelligent agentic system built with LangGraph that performs scalable LLM-powered topic modeling on large datasets (1000+ rows) from CSV/Excel files. The system provides three distinct analytical approaches for understanding and categorizing data through row-level analysis.

## Features

### Data Input
- **Supported Formats**: CSV and Excel files
- **Scale**: Efficiently handles datasets with 1000+ rows
- **Flexibility**: User-selectable columns for targeted analysis
- **Granularity**: Row-level processing for detailed insights

### Three Analysis Modes

#### 1. Bottom-Up Topic Discovery (Unsupervised)
Discovers latent themes organically present in your data.

**LangGraph Workflow:**
```
Start → Load Data → Select Columns → Row Summarization Node → 
Batch Grouping Node → Theme Extraction Node → Theme Consolidation Node → 
Topic Assignment Node → Results Visualization → End
```

**Process:**
1. **Row Summarization Node**: Generates concise summaries for each row using selected columns
2. **Batch Grouping Node**: Groups summaries into manageable batches (50-100 rows)
3. **Theme Extraction Node**: Identifies themes within each batch using LLM
4. **Theme Consolidation Node**: Merges and refines themes across all batches
5. **Topic Assignment Node**: Maps original rows to discovered topics

**Output:**
- Discovered topics with descriptions
- Topic distribution visualization
- Representative examples per topic
- Interactive topic refinement

#### 2. Top-Down Topic Classification (Supervised)
Classifies data against predefined topics/categories.

**LangGraph Workflow:**
```
Start → Load Data → Load/Define Topics → Select Columns → 
Row Summarization Node → Classification Node → Confidence Scoring Node → 
Multi-label Handler Node → Results Aggregation → End
```

**Process:**
1. **Topic Definition**: User provides existing taxonomy
2. **Row Summarization Node**: Generates summaries from selected columns
3. **Classification Node**: Assigns each row to predefined topics using LLM
4. **Confidence Scoring Node**: Evaluates classification certainty
5. **Multi-label Handler Node**: Manages ambiguous or multi-topic cases

**Output:**
- Row-level topic assignments
- Classification confidence scores
- Topic distribution across dataset
- Flagged items for review (low confidence, ambiguous)

#### 3. Root Cause Analysis
Identifies underlying causes for each data point.

**LangGraph Workflow:**
```
Start → Load Data → Select Columns → Row Analysis Node → 
Root Cause Identification Node → Cause Categorization Node → 
Pattern Recognition Node → Aggregation Node → Results Visualization → End
```

**Process:**
1. **Row Analysis Node**: Deep analysis of each individual row
2. **Root Cause Identification Node**: Extracts underlying causes using LLM
3. **Cause Categorization Node**: Groups similar root causes
4. **Pattern Recognition Node**: Identifies systemic patterns
5. **Aggregation Node**: Consolidates findings across dataset

**Output:**
- Root cause per row with evidence
- Aggregated common causes
- Frequency distribution
- Drill-down capability by cause

## LangGraph Architecture

### State Management
```python
class AnalysisState(TypedDict):
    data: pd.DataFrame
    selected_columns: List[str]
    analysis_mode: str  # "bottom_up", "top_down", "root_cause"
    row_summaries: List[str]
    batches: List[List[str]]
    themes: List[Dict]
    topics: List[Dict]
    classifications: List[Dict]
    root_causes: List[Dict]
    progress: float
    results: Dict
```

### Node Structure

**Common Nodes:**
- `load_data_node`: Reads CSV/Excel files
- `column_selector_node`: Filters selected columns
- `row_summarization_node`: Generates row-level summaries
- `progress_tracker_node`: Updates processing status

**Bottom-Up Specific Nodes:**
- `batch_grouping_node`: Creates optimal batch sizes
- `theme_extraction_node`: Identifies themes per batch
- `theme_consolidation_node`: Merges themes globally
- `topic_assignment_node`: Maps rows to topics

**Top-Down Specific Nodes:**
- `topic_definition_node`: Loads predefined taxonomy
- `classification_node`: Assigns rows to topics
- `confidence_scoring_node`: Evaluates assignments
- `multi_label_handler_node`: Manages edge cases

**Root Cause Specific Nodes:**
- `row_analysis_node`: Analyzes individual rows
- `cause_identification_node`: Extracts root causes
- `cause_categorization_node`: Groups similar causes
- `pattern_recognition_node`: Finds systemic patterns

### Conditional Edges

```python
def route_analysis_mode(state: AnalysisState) -> str:
    if state["analysis_mode"] == "bottom_up":
        return "batch_grouping_node"
    elif state["analysis_mode"] == "top_down":
        return "topic_definition_node"
    else:
        return "row_analysis_node"
```

## Technical Implementation

### Scalability Features

1. **Batch Processing**: Handles large datasets efficiently
   - Configurable batch sizes (default: 50-100 rows)
   - Parallel LLM calls within batches
   - Memory-efficient streaming

2. **Async Operations**: 
   - Non-blocking LLM API calls
   - Concurrent node execution where possible
   - Real-time progress updates

3. **Caching Strategy**:
   - Cache row summaries to avoid reprocessing
   - Store intermediate results
   - Resume capability for interrupted processes

4. **Error Handling**:
   - Retry logic for failed LLM calls
   - Graceful degradation
   - Partial results recovery

### LLM Integration

**Prompt Templates:**
- Row summarization prompts
- Theme extraction prompts
- Classification prompts
- Root cause analysis prompts

**API Optimization:**
- Rate limit management
- Token usage optimization
- Cost-effective batch sizing
- Model selection per task (GPT-4 for complex, GPT-3.5 for simple)

```

## Workflow Example

**Use Case**: Analyzing 2000 customer support tickets

1. **Upload**: Load `support_tickets.xlsx`
2. **Select Columns**: Choose "Issue Description", "Customer Feedback", "Resolution Notes"
3. **Choose Mode**:
   - **Bottom-Up**: Discover emerging issue patterns
   - **Top-Down**: Classify into Billing/Technical/Account categories
   - **Root Cause**: Identify why issues occurred (Bug/User Error/Doc Gap)
4. **Review Results**: Interactive dashboard with visualizations
5. **Export**: Download analysis results as CSV/JSON

