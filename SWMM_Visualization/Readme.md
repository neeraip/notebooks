🌧️ SWMM Visualization Toolkit
A powerful, interactive visualization suite for Storm Water Management Model (SWMM) simulations — all in a single Jupyter Notebook.

🚀 Features at a Glance
- 💡 Single Simulation Run
  Run the simulation once — use the output across all visualizations.

- 🗺️ Interactive Map Viewer
  Explore flow, depth, and volume dynamically with a time slider and clickable conduits.

- 🎞️ Flow Animation
  Watch the network flow evolve over time with color-coded animations.

- 📊 Hydrograph Explorer
  Dive into node and conduit time series with an intuitive widget.

🛠️ Getting Started

1. Environment Setup
Ensure you have Python 3.7+ installed. Then install the required packages:

pip install pyswmm folium branca matplotlib ipywidgets tqdm pyproj

2. Prepare Your Model
- Place your SWMM `.inp` file in the notebook's working directory.
- Update the `inp_path` variable in Cell 2 with your file name.

▶️ How to Use

Cell 1: ⚙️ Imports & Helpers  
Run first — loads all required libraries and helper functions.

Cell 2: 🧪 Run Simulation  
Executes the SWMM model once and stores the results.

Cell 3: 🗺️ Interactive Map  
Visualize conduit attributes dynamically with a time slider.

Cell 4: 🎬 Flow Animation  
Create a time-lapse of the flow through the network.

Cell 5: 📈 Hydrograph Widget  
Select any node or conduit and explore attribute time series interactively.

🎨 Visualization Modules

1. Interactive Map
- Choose an attribute: Flow, Depth, or Volume
- Navigate through time using the slider
- Click a conduit for detailed values at any time step

2. Flow Animation
- Auto-generates animation of the entire network
- Adjust frame_step to control speed/resolution
- Color ramp: 🟢 Low → 🔴 High flow

3. Hydrograph Widget
- Toggle between Nodes and Conduits
- Pick the element ID and attribute
- Displays inflow types for nodes (e.g., Total and Lateral Inflow)

🧠 Pro Tip
All visualizations are fully interactive and self-contained — perfect for exploring and communicating SWMM results visually and efficiently.
