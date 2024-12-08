#Iteration 1

import pandas as pd
import plotly.graph_objects as go
from datetime import datetime, timedelta
import random

#Read the csv file
cholera_data = pd.read_csv('snow_dates.csv')

# Sample data generation similar to the cholera outbreak data
date_range_start = datetime(1854, 8, 19)
num_days = 30

# Create a DataFrame with sample data
data = {
    "date": [date_range_start + timedelta(days=i) for i in range(num_days)],
    "attacks": [random.randint(0, 10) for _ in range(num_days)],
    "deaths": [random.randint(0, 5) for _ in range(num_days)]
}
cholera_data = pd.DataFrame(data)

# Create an interactive stacked bar chart with Plotly
fig = go.Figure()

# Add bar for attacks
fig.add_trace(go.Bar(
    x=cholera_data['date'],
    y=cholera_data['attacks'],
    name='Attacks',
    hovertemplate='Date: %{x}<br>Attacks: %{y}<extra></extra>'
))

# Add bar for deaths
fig.add_trace(go.Bar(
    x=cholera_data['date'],
    y=cholera_data['deaths'],
    name='Deaths',
    hovertemplate='Date: %{x}<br>Deaths: %{y}<extra></extra>'
))

# Update layout for better visualization
fig.update_layout(
    title='Interactive Stacked Bar Chart of Cholera Attacks and Deaths (1854)',
    xaxis=dict(title='Date'),
    yaxis=dict(title='Count'),
    barmode='stack',
    hovermode='x unified',
    template='plotly_white'
)

# Show the interactive plot
fig.show()

#Iteration 2

import pandas as pd
import plotly.graph_objects as go
from datetime import datetime, timedelta
import random

#Read the csv file
cholera_data = pd.read_csv('snow_dates.csv')
cholera_data = pd.read_csv('deaths.csv')

# Sample data generation for a three-month period
date_range_start = datetime(1854, 8, 1)
num_days = 90  # Covering three months

# Create a DataFrame with sample data
data = {
    "date": [date_range_start + timedelta(days=i) for i in range(num_days)],
    "attacks": [random.randint(20, 50) for _ in range(num_days)],
    "deaths": [random.randint(5, 20) for _ in range(num_days)]
}
cholera_data = pd.DataFrame(data)

# Aggregate data weekly to simplify the polar chart visualization
cholera_data['week'] = cholera_data['date'].dt.to_period('W').apply(lambda r: r.start_time)
weekly_data = cholera_data.groupby('week').sum().reset_index()

# Prepare data for plotting
labels = weekly_data['week'].dt.strftime('%b %d').tolist()
attacks = weekly_data['attacks'].tolist()
deaths = weekly_data['deaths'].tolist()

# Create the base figure for polar area chart
fig = go.Figure()

# Add trace for attacks (initially shown)
fig.add_trace(go.Barpolar(
    r=attacks,
    theta=labels,
    name='Attacks',
    marker=dict(line_color='black', line_width=2),
    hoverinfo='theta+r+name'
))

# Add trace for deaths (initially hidden)
fig.add_trace(go.Barpolar(
    r=deaths,
    theta=labels,
    name='Deaths',
    marker=dict(line_color='black', line_width=2),
    hoverinfo='theta+r+name',
    visible=False  # Set to hidden initially
))

# Update layout with dropdown menu
fig.update_layout(
    title='Cholera Attacks and Deaths (1854) - Select Data',
    polar=dict(
        radialaxis=dict(visible=True, range=[0, max(max(attacks), max(deaths)) + 10])
    ),
    updatemenus=[
        dict(
            buttons=[
                dict(
                    label="Attacks",
                    method="update",
                    args=[{"visible": [True, False]},  # Show only Attacks
                          {"title": "Cholera Attacks (1854)"}]
                ),
                dict(
                    label="Deaths",
                    method="update",
                    args=[{"visible": [False, True]},  # Show only Deaths
                          {"title": "Cholera Deaths (1854)"}]
                ),
                dict(
                    label="Both",
                    method="update",
                    args=[{"visible": [True, True]},  # Show Both Attacks and Deaths
                          {"title": "Cholera Attacks and Deaths (1854)"}]
                )
            ],
            direction="down",
            showactive=True,
        )
    ]
)

# Show the interactive plot
fig.show()

#Iteration 3

import pandas as pd
import plotly.graph_objects as go
import plotly.express as px
import requests
from datetime import datetime
import random

#Read the csv file
cholera_data = pd.read_csv('snow_dates.csv')
cholera_data = pd.read_csv('deaths.csv')

def fetch_real_time_data():
    return create_sample_data()

def create_sample_data(num_samples=50):
    """Generate sample data for the visualization"""
    data = {
        "date": [datetime.now() - pd.Timedelta(days=random.randint(0, 30)) for _ in range(num_samples)],
        "latitude": [51.513 + random.uniform(-0.01, 0.01) for _ in range(num_samples)],
        "longitude": [-0.136 + random.uniform(-0.01, 0.01) for _ in range(num_samples)],
        "attacks": [random.randint(1, 10) for _ in range(num_samples)],
        "deaths": [random.randint(1, 5) for _ in range(num_samples)]
    }
    return pd.DataFrame(data)

def create_real_time_cholera_map():
    # Fetch real-time data
    cholera_data = fetch_real_time_data()
    
    # Create the map figure using Plotly Express for real geolocation mapping
    fig = px.scatter_mapbox(
        cholera_data,
        lat="latitude",
        lon="longitude",
        size="attacks",
        color="deaths",
        hover_name="date",
        hover_data={"attacks": True, "deaths": True, "latitude": False, "longitude": False},
        color_continuous_scale="Viridis",
        zoom=13,
        height=800
    )
    
    # Update map layout
    fig.update_layout(
        mapbox_style="open-street-map",
        title={
            'text': 'Real-Time Map of Cholera Outbreaks',
            'y':0.95,
            'x':0.5,
            'xanchor': 'center',
            'yanchor': 'top'
        },
        margin=dict(l=20, r=20, t=100, b=100)
    )
    
    return fig

def main():
    # Create and display the real-time map
    fig = create_real_time_cholera_map()
    fig.show()

if _name_ == "_main_":
    main()
