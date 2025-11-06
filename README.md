## NMT: English ↔ Telugu with OpenNMT‑py


## Project Structure

- Root files and directories
  - config.yaml — OpenNMT‑py model and training configuration
  - dataset — All datasets and derived corpora
    - raw
      - en-te.csv — Raw bilingual CSV (English–Telugu)
    - parallel_copora — Cleaned, aligned parallel corpora (preprocessing output)
      - en-te.en — English side for English→Telugu
      - en-te.te — Telugu side for English→Telugu
      - te-en.en — English side for Telugu→English
      - te-en.te — Telugu side for Telugu→English
    - subword_corpora — Subworded corpora (SentencePiece output)
      - en-te.en.subword
      - en-te.te.subword
    - train_test_split — Train, Dev, and Test splits (subworded)
      - en-te.en.subword.train, .dev, .test
      - en-te.te.subword.train, .dev, .test
  - models — Checkpoints, SentencePiece models, and exported CTranslate2 model
    - NMT_models — OpenNMT‑py checkpoints and logs
      - small_model.en-te_step_500.pt
      - small_model.en-te_step_1000.pt
      - train.log
    - SW_model — SentencePiece models and vocabularies
      - source.model, source.vocab
      - target.model, target.vocab
    - ctranslate2_model — Final exported model for fast inference
      - config.json, model.bin
      - source_vocabulary.json, target_vocabulary.json
  - notebooks — Executable steps with detailed markdown explanations
    - preprocessing.ipynb — Data cleaning, alignment, and tagging
    - subwording.ipynb — SentencePiece training and subwording
    - training.ipynb — OpenNMT‑py training and CTranslate2 export
  - ouput.te — Example output file (sample inference/output)

---

### 1) Preprocessing 
- Load the bilingual English–Telugu data from the raw CSV in the dataset folder.
- Extract and clean parallel sentences.
- Add multilingual target tags in the format <2target_language_domain> for multilingual NMT control.
- Save aligned parallel files for both directions inside the parallel corpora directory using the file naming convention en-te.en, en-te.te, te-en.en, and te-en.te.
- Notebook: preprocessing.ipynb (includes detailed markdown explanations of each step)

### 2) Subwording 
- Use the MT‑Preparation workflow to train SentencePiece models.
- Apply unigram subword tokenization for English and Telugu.
- Split into Train, Dev, and Test (with 2,000 samples each for Dev and Test).
- Save the subworded corpora and the splits in the subword corpora and train_test_split directories.
- Notebook: subwording.ipynb (with detailed markdown walkthroughs and rationale)

### 3) Training and Export 
- Define a compact Transformer for testing in the configuration file with 2 encoder/decoder layers, 4 attention heads, and hidden size 256.
- Build the vocabulary from subworded data and train using OpenNMT‑py (GPU recommended).
- Save checkpoints and logs in the NMT models directory.
- Export the best model to CTranslate2 for fast inference and save it in the ctranslate2_model directory.
- Notebook: training.ipynb (step‑by‑step instructions and explanations in markdown)

---

## Setup

### Prerequisites
- Python <=3.11  (CUDA optional but recommended for GPU training)
- A CUDA‑enabled GPU for faster training and inference (optional)
- Jupyter to run the notebooks (optional but recommended)

### Install Dependencies
- Use your Python environment manager of choice to install: OpenNMT‑py, SentencePiece, CTranslate2, Sacremoses, Torch, TorchVision, and TorchAudio.
- If using CUDA, install the appropriate CUDA build of PyTorch according to the official PyTorch instructions.

---

## Reproducing the Pipeline

### 1) Prepare Data
- Place the bilingual CSV in the dataset/raw folder with parallel English–Telugu rows.

### 2) Run Notebooks (preferred)
- Execute the notebooks in this order: preprocessing, subwording, training.
- Each notebook contains detailed markdown explanations, rationale, and cell‑by‑cell guidance.

### 3) Optional: Command‑Line Workflow
- Build the vocabulary using the OpenNMT‑py vocabulary builder configured by the configuration file.
- Train the model using the OpenNMT‑py training command with the same configuration.
- Convert the resulting checkpoint to CTranslate2 format using the converter tool, saving outputs to the ctranslate2_model directory.

---

## Using the Exported Model (CTranslate2)

- Load the exported model directory with the CTranslate2 Translator.
- Use the SentencePiece source and target models from the SW_model directory to tokenize and detokenize text.
- For English to Telugu, prepend the appropriate target language tag (for example, <2te_general>) to the tokenized input before translation. For Telugu to English, use the corresponding English tag (for example, <2en_general>).
- Batch multiple sentences by preparing multiple tokenized sequences and passing them to the batch translation interface.

---

## Configuration Notes

- The configuration file controls model size, training hyperparameters, data paths, and vocabulary settings.
- SentencePiece model files reside in the SW_model directory and must match the data used during training.
- Training progress and validation metrics are recorded in the training log within the NMT models directory.









