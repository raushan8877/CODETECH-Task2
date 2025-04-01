# CODETECH-Task2
# Install required library
!pip install fpdf

# Import libraries
import pandas as pd
from fpdf import FPDF
from google.colab import files

# Upload CSV file
uploaded = files.upload()

# Check if any file is uploaded
if not uploaded:
    print("No file uploaded.")
else:
    # Load data from the uploaded CSV file
    def load_data(file_path):
        try:
            data = pd.read_csv(file_path)
            if data.empty:
                print("Error: The CSV file is empty.")
                return None
            return data
        except FileNotFoundError:
            print(f"Error: File '{file_path}' not found.")
            return None
        except pd.errors.EmptyDataError:
            print("Error: No data found in the file.")
            return None
        except pd.errors.ParserError:
            print("Error: File content is not in the correct format.")
            return None
        except Exception as e:
            print(f"Error loading file: {e}")
            return None

    # Analyze data
    def analyze_data(data):
        if data is None or data.empty:
            print("Error: No data available for analysis.")
            return None
        return data.describe()  # Summary statistics

    # Generate PDF report
    def generate_pdf_report(summary, output_file):
        if summary is None:
            print("Error: No summary data available for report generation.")
            return
        
        pdf = FPDF()
        pdf.set_auto_page_break(auto=True, margin=15)
        pdf.add_page()
        pdf.set_font("Arial", "B", 16)
        pdf.cell(200, 10, "Data Analysis Report", ln=True, align='C')
        pdf.ln(10)
        
        pdf.set_font("Arial", size=12)
        for col in summary.columns:
            pdf.cell(200, 10, f"Statistics for {col}", ln=True, align='L')
            pdf.set_font("Arial", size=10)
            for stat, value in summary[col].items():
                pdf.cell(200, 7, f"{stat}: {value:.2f}", ln=True, align='L')
            pdf.ln(5)
            pdf.set_font("Arial", size=12)
        
        pdf.output(output_file)
        print(f"Report saved as {output_file}")

    # Main execution
    uploaded_file = next(iter(uploaded))  # Gets the first uploaded file name
    output_file = "report.pdf"
    
    data = load_data(uploaded_file)
    if data is not None:
        summary = analyze_data(data)
        generate_pdf_report(summary, output_file)
