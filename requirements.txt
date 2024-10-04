import json
import streamlit as st
import datetime
from pathlib import Path

# File to store the data
DATA_FILE = Path("blog_data.json")

def load_data():
    if DATA_FILE.exists():
        with open(DATA_FILE, "r") as f:
            return json.load(f)
    return []

def save_data(data):
    with open(DATA_FILE, "w") as f:
        json.dump(data, f, indent=2)

def get_entries_by_week(week):
    data = load_data()
    return [item for item in data if item['week'] == week]

def add_entry(title, textruta, taggar, mood, date, week):
    data = load_data()
    new_entry = {
        "week": week,
        "tags": taggar,
        "title": title,
        "textruta": textruta,
        "mood": mood,
        "datum": date,
    }
    data.append(new_entry)
    save_data(data)

def huvudsida():
    st.subheader('Se inlägg')

    allowed_weeks = [str(week) for week in range(40, 49) if week != 44]
    week = st.selectbox("Välj vecka att visa", allowed_weeks)

    items = get_entries_by_week(int(week))

    if not items:
        st.info(f"Inga inlägg hittades för vecka {week}.")
    else:
        for item in items:
            st.write(f"**Vecka:** {item['week']}")
            st.write(f"**Datum:** {item['datum']}")
            st.write(f"**Titel:** {item['title']}")
            st.write(f"**Innehåll:** {item['textruta']}")
            st.write(f"**Humör:** {item['mood']}")
            st.write(f"**Tags:** {', '.join(item['tags'])}")
            st.write('~~~~')

st.header('Zakkis bloggsida')

today = datetime.datetime.today()
currentweek = today.isocalendar()[1]

st.write(f'Välkommen Zakk! Dagens datum: {today.date()}, Vecka: {currentweek}. Ha en trevlig dag!')

st.subheader('Vänligen välj vecka nedan')
week = st.selectbox('Veckoval', [str(w) for w in range(41, 50) if w != 44])

title = st.text_input('Välj titel här')
textruta = st.text_area('')
st.subheader('Vänligen välj taggar här')
taggar = st.multiselect('Tagg alternativ',
                        ['#Koding', '#Kundtjänst', '#Felsökning', "#Teamwork",
                         '#Enskillt', '#Lugn', '#Stressigt', '#Nöjd', '#Handledare'])
mood = st.selectbox('Välj humör', ['😄', '🙂', '😐', '😕', '😢'])

if st.button('Spara'):
    if not title or not textruta:
        st.error('Titel och innehåll är obligatoriska')
    else:
        add_entry(title, textruta, taggar, mood, str(today.date()), int(week))
        st.success('Inlägg sparat!')

huvudsida()
