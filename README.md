import streamlit as st
import pandas as pd
import altair as alt

# Load your datasets
DATA_URL_1 = "https://raw.githubusercontent.com/iantonios/dsc205/main/CT-affordable-housing-2011-2022.csv"
DATA_URL_2= "https://raw.githubusercontent.com/iantonios/dsc205/main/CT-towns-income-census-2020.csv"

#Data needs to be pulled and for each chart, 
#This function to ensure that the data is well understood (and not causing errors as it will help)

def load_data(url):
    try:
        df = pd.read_csv(url)
        print(f"DataFrame loaded successfully from {url} : {df.columns}") #This provides you that the Data has loaded correctly
        return df
    except Exception as e:
        print(f"Error loading data from {url}: {e}")
        return None
#This tests the load for all.

def create_housing_chart(housing_df):
    if housing_df is None:
        return None

    chart = alt.Chart(housing_df).mark_line().encode(
        x=alt.X('town', axis=alt.Axis(labelAngle=-45)),  # Rotates x-axis labels
        y='govt_assisted',
        tooltip=['town', 'govt_assisted', 'rental_assistance','other']
    ).properties(
        title='Government Assisted',
        width=600,
        height=400
    )
    return chart

def create_housing_percent_chart(housing_df):
    if housing_df is None:
        return None
    base = alt.Chart(housing_df).encode(
        alt.X('Town:N', axis=alt.Axis(labelAngle=-45))
    )
    # We add the labels by encoding the text property with the same value as the bar, and then setting the baseline.
    bar = base.mark_bar().encode(
        y='percentage_housing_units:Q'
    )
    text = base.mark_text(dx=-12, dy=-8, color='white').encode(
        y='percentage_housing_units:Q',
        text=alt.Text('percentage_housing_units:Q', format='.1f')
    )

    chart = (bar + text).properties(title= "Percentage of Affordable Housing Units by City in 2014, 2018, and 2022")
    return chart

def create_income_chart(income_df):
        if income_df is None:
           return None
        return alt.Chart(income_df).mark_boxplot().encode(
            x=alt.X('County', axis=alt.Axis(labelAngle=-45)),
            y='Number of households',
            tooltip=['County','Place','Type',  'Per capita income'] #These can be anything!
        ).properties(
        title='Household vs Count (2020)',
        width=600,
        height=400
)


# Main App
def main():
    #Page Config
    st.set_page_config(
        page_title="Affordable Housing Dashboard: Data Exploration",
        layout="wide",
        initial_sidebar_state="expanded",
    )
    st.title("Affordable Housing in Connecticut: An Exploration")
    #st.markdown("This simple Streamlit app explores Affordable Housing data.  Select the visuals below for more info")

    # Load DataFrames
    housing_df = load_data(DATA_URL_1)
    income_df = load_data(DATA_URL_2)

    # Create charts
    housing_gchart = create_housing_chart(housing_df)
    income_chart = create_income_chart(income_df)

        #Load to
    bar_chart = df_20014.groupby("town")

  st.title('Affordable Housing in Connecticut: Data Exploration')
    # Display charts if the DataFrame is loaded successfully
        #Load to select
    select = st.selectbox('Select a visual chart!', ['Proportions of Affordable Housing Units', 'Household vs Count', 'Government Assisted Distribution'])
    if select == 'Government Assisted Distribution':
        if housing_gchart is not None: #If statement used here so the data won't continue with "If there are no data load"
            st.altair_chart(housing_gchart)
    elif select == "Household vs Count":
        if income_chart is not None:
            st.altair_chart(income_chart, use_container_width=True) #This chart uses that information
    elif select == "Proportions of Affordable Housing Units":

       st.write("Code for this has not been added! Please request to add those here and what data would make them shine!")


if __name__ == "__main__":
    main()
