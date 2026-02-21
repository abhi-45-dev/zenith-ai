
import streamlit as st
import os
from dotenv import load_dotenv
from langchain_google_genai import ChatGoogleGenerativeAI

from langchain_classic.memory import ConversationBufferMemory
from langchain_classic.chains import ConversationChain


load_dotenv()


st.set_page_config(page_title="Zenith AI", page_icon="⚡")

st.markdown("""
    <style>
    /* Gemini UI: User on Right, Zenith on Left */
    .stChatMessage:has([data-testid="stChatMessageAvatarUser"]) {
        flex-direction: row-reverse;
        text-align: right;
    }
    </style>
""", unsafe_allow_html=True)

st.title("⚡ Zenith AI")
st.caption("A Gemini 3 project by **S.ABHIRAM**")


llm = ChatGoogleGenerativeAI(model="gemini-flash-latest", temperature=0.7)


if "memory" not in st.session_state:
    st.session_state.memory = ConversationBufferMemory()

if "messages" not in st.session_state:
    st.session_state.messages = [{"role": "assistant", "content": "Zenith here, how can I help you today?"}]


conversation = ConversationChain(llm=llm, memory=st.session_state.memory)


for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

if prompt := st.chat_input("Message Zenith..."):
    st.session_state.messages.append({"role": "user", "content": prompt})
    with st.chat_message("user"):
        st.markdown(prompt)

    with st.chat_message("assistant"):
        with st.spinner("Zenith is thinking..."):
            try:
                # Use the chain to get a response with memory
                response = conversation.predict(input=prompt)
                st.markdown(response)
                st.session_state.messages.append({"role": "assistant", "content": response})
            except Exception as e:
                st.error(f"Error: {e}")
