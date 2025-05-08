# -Real-Time-Weather-Dashboard
import streamlit as st
import requests
import datetime
import plotly.graph_objs as go

# Replace with your actual OpenWeatherMap API key
API_KEY = "949cd2efd73155cab937e7e6ab7e970d"

def get_weather(city):
    url = f"http://api.openweathermap.org/data/2.5/forecast?q=Mumbai&appid=949cd2efd73155cab937e7e6ab7e970d&units=metric"
    response = requests.get(url)
    data = response.json()

    # Check for errors in the API response
    if data.get("cod") != "200":
        st.error(f"Error: {data.get('message')}")
        return None

    return data

def plot_forecast(data):
    times = [datetime.datetime.fromtimestamp(entry["dt"]) for entry in data["list"]]
    temps = [entry["main"]["temp"] for entry in data["list"]]

    fig = go.Figure()
    fig.add_trace(go.Scatter(x=times, y=temps, mode='lines+markers', name='Temperature'))
    fig.update_layout(title="5-Day Forecast", xaxis_title="Time", yaxis_title="Temperature (°C)")
    st.plotly_chart(fig)

st.title("🌤️ Real-Time Weather Dashboard")
city = st.text_input("Enter city", "Mumbai")

if city:
    # Make the request to the API and get data
    data = get_weather(city)
    if data:
        # Display current weather
        current = data["list"][0]
        st.metric("Temperature", f"{current['main']['temp']} °C")
        st.metric("Humidity", f"{current['main']['humidity']} %")
        st.metric("Wind Speed", f"{current['wind']['speed']} m/s")
        st.image(f"http://openweathermap.org/img/wn/{current['weather'][0]['icon']}@2x.png")
        st.subheader(current['weather'][0]['description'].title())

        # Plot the 5-day forecast
        plot_forecast(data)
