import streamlit as st
from google import genai
from google.genai import types
from PIL import Image
import io

# 1. App Configuration & Branding
st.set_page_config(page_title="AURA Jewelry Suite", layout="wide")
st.title("💎 AURA Jewelry Suite")
st.markdown("### AI-Powered Jewelry Analyzer & Enhancer")

# 2. Sidebar for Settings
with st.sidebar:
    st.header("Control Panel")
    api_key = st.text_input("Enter Gemini API Key", type="password")
    style_preset = st.selectbox("Select Editorial Style", 
                                ["Champagne Minimal", "Royal Gold & Marble", "Midnight Velvet", "Soft Studio Lighting"])
    st.info("AURA uses multimodal analysis to preserve your original jewelry design.")

# 3. Image Upload Section
uploaded_file = st.file_uploader("Upload raw jewelry photo (e.g., Bangles, Jhumkas)", type=["jpg", "png", "jpeg"])

if uploaded_file and api_key:
    client = genai.Client(api_key=api_key)
    raw_img = Image.open(uploaded_file)
    
    # 4. Side-by-Side Interface
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("Original Photo")
        st.image(raw_img, use_container_width=True)

    # 5. The "Thinking" & Generation Button
    if st.button("Generate Luxury Render"):
        with st.status("AURA is thinking...", expanded=True) as status:
            st.write("🔍 Analyzing metal texture and stone clarity...")
            
            # Step A: Detailed Analysis
            analysis_res = client.models.generate_content(
                model="gemini-1.5-flash",
                contents=["Describe this jewelry in detail for an editorial brief. Focus on metals and stones.", raw_img]
            )
            detected_brief = analysis_res.text
            st.write(f"**Brief Created:** {detected_brief[:100]}...")
            
            st.write("✨ Applying cinematic lighting and focus stacking...")
            
            # Step B: Enhancement Render
            final_prompt = f"Professional luxury product photo: {detected_brief}. Style: {style_preset}. 4k, razor sharp focus, cinematic shadows."
            
            response = client.models.generate_content(
                model='gemini-2.5-flash-image', 
                contents=[final_prompt, raw_img],
                config=types.GenerateContentConfig(response_modalities=["IMAGE"])
            )
            status.update(label="Enhancement Complete!", state="complete", expanded=False)

        with col2:
            st.subheader("AURA Styled Creation")
            for part in response.parts:
                if part.inline_data:
                    final_image = part.as_image()
                    st.image(final_image, use_container_width=True)
                    
                    # Download Option
                    buf = io.BytesIO()
                    final_image.save(buf, format="PNG")
                    st.download_button("Download Editorial Render", buf.getvalue(), "aura_render.png")
