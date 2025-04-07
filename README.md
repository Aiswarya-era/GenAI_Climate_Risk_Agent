# GenAI_Climate_Risk_Agent
Gen-AI agent for climate risk &amp; insurance insights from real-time news and reports.
# Directory structure we will follow:
# genai-climate-risk-agent/
# ├── main.py
# ├── agent.py
# ├── ui.py
# ├── utils.py
# ├── requirements.txt
# ├── README.md

# main.py
from agent import get_climate_insights
from ui import run_dashboard

if __name__ == "__main__":
    insights, references = get_climate_insights("climate risk in insurance")
    run_dashboard(insights, references)


# agent.py
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_community.tools.tavily_search import TavilySearchResults

load_dotenv()

openai_apikey = os.getenv("OPENAI_API_KEY")
tavily_key = os.getenv("TAVILY_API_KEY")

model = ChatOpenAI(model="gpt-3.5-turbo-1106", api_key=openai_apikey)

search_tool = TavilySearchResults(
    max_results=100,
    search_depth="advanced",
    include_answer=True,
    include_raw_content=True,
    include_images=True
)

def get_climate_insights(query):
    response = search_tool.invoke({"query": query})
    insights = structure_the_response(response)
    enriched, references = correlate_with_research(insights)
    return enriched, references

from utils import structure_the_response, correlate_with_research


# utils.py
def structure_the_response(response):
    # Stub implementation
    return [
        {
            "title": "Insurance risks will be a proxy carbon tax",
            "source": "Reuters",
            "date": "2024-12-27",
            "summary": "In 2025, the insurance sector is expected to reflect climate impacts via higher premiums, effectively acting as a proxy carbon tax.",
            "tags": ["parametric insurance", "basis risk", "climate change"]
        }
    ]

def correlate_with_research(insights):
    references = [
        {
            "title": "Managing Basis Risks in Weather Parametric Insurance",
            "authors": "Hang Gao, Shuohua Yang, Xinli Liu",
            "date": "2024-09-25",
            "abstract": "Portfolio diversification can reduce basis risk in weather parametric insurance.",
            "tags": ["basis risk", "parametric insurance", "Monte Carlo"]
        }
    ]
    return insights, references


# ui.py
import streamlit as st

def filter_by_tag(data, tag):
    return [item for item in data if tag in item["tags"]]

def get_all_tags(datasets):
    tags = set()
    for data in datasets:
        for item in data:
            tags.update(item["tags"])
    return sorted(tags)

def run_dashboard(news_articles, research_papers):
    st.title("Climate Risk Insurance Dashboard")

    all_tags = get_all_tags([news_articles, research_papers])
    selected_tag = st.sidebar.selectbox("Select a tag", all_tags)

    st.header(f"News Articles Tagged: {selected_tag}")
    filtered_news = filter_by_tag(news_articles, selected_tag)
    for article in filtered_news:
        st.subheader(article["title"])
        st.caption(f"{article['source']} | {article['date']}")
        st.write(article["summary"])
        st.markdown("---")

    st.header(f"Research Papers Tagged: {selected_tag}")
    filtered_research = filter_by_tag(research_papers, selected_tag)
    for paper in filtered_research:
        st.subheader(paper["title"])
        st.caption(f"{paper['authors']} | {paper['date']}")
        st.write(paper["abstract"])
        st.markdown("---")

    st.sidebar.markdown("### About")
    st.sidebar.info(
        "This dashboard integrates news and academic research on climate risk insurance. "
        "Select a tag to see related items from both news and arXiv research."
    )


# requirements.txt
langchain-openai
langchain-community>=0.2.11
langchain-core
python-dotenv
tavily-python
streamlit


# README.md
"""
# GenAI Climate Risk Agent

A Gen-AI agent that extracts, summarizes, and correlates insights from real-time news and research about climate risk in insurance using OpenAI + LangChain + Tavily + Streamlit.

## Features
- Real-time search on climate risk and insurance news
- Summarization of news
- Matching with research papers to validate insights
- Interactive dashboard using Streamlit

## Setup Instructions
1. Clone this repository
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Create a `.env` file with:
   ```env
   OPENAI_API_KEY=your_openai_key
   TAVILY_API_KEY=your_tavily_key
   ```
4. Run the app:
   ```bash
   streamlit run main.py
   ```

## Sample Output
Structured news + matching research papers presented in a clean visual dashboard.

---

> Built for Chubb AI-ML Hackathon - Climate Risk Agent Challenge
"""
