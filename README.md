# Mental Health Companion chatbot - Sneha Maheria
A conversational AI chatbot that provides empathetic mental health support. It uses Retrieval-Augmented Generation (RAG) to ground its responses in real psychological literature, and detects the user's emotional state to respond with appropriate sensitivity and care.

This isn't a replacement for therapy — it's a calm, non-judgmental space where someone can talk about how they're feeling and get a thoughtful, informed response.

---

## How to Run (Google Colab)

Everything runs on Google Colab. You don't need to set anything up locally.

### What you need before starting

- The `combined_chunks.pkl` file
- The `embeddings.npy` file

These two files are the preprocessed knowledge base (generated from the source books using `extraction_of_chunks_and_embeddings.py`). If you already have them, you're ready to go.

### Steps

1. Open `Main_logic_chatbot.ipynb` in Google Colab
2. Upload `combined_chunks.pkl` and `embeddings.npy` to the Colab session — you can do this by clicking the folder icon on the left sidebar, then the upload button
3. Run all cells (Runtime → Run all)
4. Wait for all models to load — this takes a few minutes since it's downloading TinyLlama and the emotion classifier
5. Once the last cell runs, Gradio will print a public link in the output — open that link to use the chatbot

That's it. The Gradio interface runs in your browser and the model runs on Colab's GPU.

---

## If you need to regenerate the .pkl and .npy files

If you don't have `combined_chunks.pkl` and `embeddings.npy`, you need to run the preprocessing script first:

1. Open `extraction_of_chunks_and_embeddings.py` in a new Colab notebook
2. Upload both source PDFs to the Colab session:
   - `Fundamentals-of-Psychological-Disorders.pdf`
   - `Psychology_of_Human_Emotion.pdf`
3. Run the script — it will extract, clean, chunk, and embed the text
4. When it finishes, Colab will automatically download `combined_chunks.pkl` and `embeddings.npy` to your computer
5. Use those files in the steps above

---

## Data Sources

The knowledge base was built from two books:

- **Fundamentals of Psychological Disorders** — covers a wide range of psychological conditions, symptoms, and clinical frameworks
- **Psychology of Human Emotion** — explores how emotions work, their role in behaviour, and the science behind emotional experience

Relevant chapters were extracted, cleaned, split into chunks of up to 400 tokens, and embedded using `all-MiniLM-L6-v2`.

> The source PDFs are not included in this repository. You'll need your own copies to regenerate the knowledge base.

---

## Project Structure

```
AI Mental Health Companion project/
│
├── extraction_of_chunks_and_embeddings.py   # Run this once to generate the .pkl and .npy files
├── Main_logic_chatbot.ipynb                 # Main chatbot notebook — run this on Colab
├── combined_chunks.pkl                      # Preprocessed text chunks (upload to Colab)
├── embeddings.npy                           # Sentence embeddings (upload to Colab)
└── requirements.txt                         # Dependencies (Colab installs these automatically)
```

---

## How It Works

When a user sends a message, the chatbot does this in sequence:

1. **Emotion detection** — classifies the emotional tone of the message (sadness, fear, anger, joy, etc.) using a DistilRoBERTa model
2. **Semantic search** — encodes the query and finds the top 4 most similar chunks from the knowledge base using cosine similarity
3. **Relevance check** — if the best match scores below the threshold (0.45), the bot declines to answer rather than making something up
4. **Context extraction** — pulls real sentences from the retrieved chunks to use as grounding context for the response
5. **Response generation** — passes everything to TinyLlama-1.1B with a structured prompt
6. **Cleanup** — strips out any leaked prompt content and trims to the last complete sentence

---

## Models Used

| Model | Purpose |
|-------|---------|
| `all-MiniLM-L6-v2` | Sentence embeddings for semantic search |
| `j-hartmann/emotion-english-distilroberta-base` | Emotion classification |
| `TinyLlama/TinyLlama-1.1B-Chat-v1.0` | Response generation |

All models are loaded from HuggingFace and downloaded automatically when you run the notebook.

---

## Limitations

- This chatbot is not a substitute for professional mental health support. It's a research and learning project.
- Response quality depends on Colab's available GPU. On CPU it will work but generation will be slow.
- TinyLlama is a small 1.1B parameter model — responses are generally good but can occasionally be repetitive or miss nuance.
- The knowledge base is limited to the two source books, so queries too far outside that scope will be filtered out.
