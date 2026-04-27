import streamlit as st
import requests
from bs4 import BeautifulSoup
import pandas as pd

st.set_page_config(page_title="NW Multi-Service Finder", page_icon="🏢")
st.title("🏢 Local Business & Barter Finder")

# 1. NEW CATEGORIES: APPLIANCE & PROPERTY
PRO_CATEGORIES = [
    "Appliance Assembly", "Appliance Delivery", "Appliance Installation",
    "Property Management", "Property Care", "Property Cleanup",
    "Remodeling", "Maintenance Tech", "Make Ready"
]

# 2. BARTER & TRADE FOCUS
BARTER_KEYWORDS = ["Barter", "Trade", "Trade for Vehicle", "Swap", "Work for Trade"]

# Target zone (The Loop)
TARGET_TOWNS = ["Olympia", "Lacey", "Tumwater", "McCleary", "Shelton", "Bremerton", "Silverdale", "Grapeview"]

def scrape_board(query, area, section="lbg"):
    """
    section='lbg' is labor gigs. 
    section='bar' is specifically the Craigslist barter section.
    """
    url = f"https://{area}.craigslist.org/search/{section}?query={query}"
    headers = {"User-Agent": "Mozilla/5.0"}
    try:
        response = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(response.text, "html.parser")
        posts = []
        for post in soup.select(".cl-static-search-result"):
            title = post.select_one(".title").text if post.select_one(".title") else ""
            link = post.select_one("a")["href"] if post.select_one("a") else "#"
            posts.append({"Area": area.title(), "Title": title, "URL": link})
        return posts
    except:
        return []

# Sidebar Navigation
mode = st.sidebar.radio("Business Focus:", ["Pro-Services", "Barter & Trade"])

if mode == "Pro-Services":
    selected_query = st.selectbox("Select Service Line:", PRO_CATEGORIES)
    if st.button(f"Search for {selected_query} Jobs"):
        # Scans both Labor and General sections for professional property jobs
        results = scrape_board(selected_query, "olympic") + scrape_board(selected_query, "seattle")
        if results:
            for res in results:
                with st.expander(f"{res['Area']} - {res['Title']}", expanded=True):
                    st.markdown(f"[**View Lead Details ↗️**]({res['URL']})")
        else:
            st.warning("No matches found. Try 'Maintenance' for property management leads.")

elif mode == "Barter & Trade":
    st.subheader("Search for Non-Cash Deals")
    selected_barter = st.selectbox("What are you willing to trade for?", ["General Trade", "Vehicle Trade", "Tools/Gear"])
    
    if st.button("Search Barter Boards"):
        # Hits the 'bar' (Barter) section specifically
        results = scrape_board("", "olympic", section="bar") + scrape_board("", "seattle", section="bar")
        if results:
            for res in results:
                # Highlight anything mentioning vehicles if that was the focus
                if "vehicle" in res['Title'].lower() or "car" in res['Title'].lower() or "truck" in res['Title'].lower():
                    st.success(f"POTENTIAL VEHICLE TRADE: {res['Title']}")
                    st.write(f"[Link to Details]({res['URL']})")
                else:
                    st.write(f"{res['Title']} - [View]({res['URL']})")
        else:
            st.info("No current barter posts found in the South Sound.")
