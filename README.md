# Solana Smart Contract Auditor LLM

## Description

The Solana Smart Contract Auditor LLM is a tool designed to audit Solana smart contracts for common vulnerabilities. It utilizes the ArmurAI/solana-smart-contract-auditor language model to analyze the smart contract code and identify potential security issues.

## Features

- **Automated Audit**: Quickly scans Solana smart contract code to find common vulnerabilities.
- **Powered by AI**: Leverages the ArmurAI model, trained specifically for auditing Solana smart contracts.
- **Efficient Processing**: Utilizes BitsAndBytes for efficient model quantization and execution.

## Installation

1. Clone the repository:
   ```
   git clone <repository-url>
   ```
2. Install the required dependencies:
   ```
   pip install streamlit transformers torch
   ```

## Usage

1. Run the Streamlit application:
   ```
   streamlit run app.py
   ```
2. Open the provided local URL in your web browser.
3. Enter the smart contract code in the text area provided on the web interface.
4. The tool will analyze the code and display any identified vulnerabilities.

## Code Example

The application is structured as follows:

- Import necessary libraries
- Load the ArmurAI/solana-smart-contract-auditor model with specific configurations
- Define a function to analyze the smart contract code
- Set up a Streamlit interface for user input and display the audit results

```python
import streamlit as st
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer, BitsAndBytesConfig

model_name = "ArmurAI/solana-smart-contract-auditor"
bnb_config = BitsAndBytesConfig(load_in_4bit=True, bnb_4bit_quant_type="nf4", bnb_4bit_compute_dtype=torch.float16)

model = AutoModelForCausalLM.from_pretrained(model_name, quantization_config=bnb_config, trust_remote_code=True)
model.config.use_cache = False

tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
tokenizer.pad_token = tokenizer.eos_token

def maker(code):
    example = ''' you are an assistant State which of the popular vulnerabilities  are present in the following smart contract
    ### Human :
    {}
    ### Assistant:
    '''
    text = example.format(code)
    device = "cuda:0"

    inputs = tokenizer(text, return_tensors="pt").to(device)
    outputs = model.generate(**inputs, max_new_tokens=150)
    output_text = tokenizer.decode(outputs[0], skip_special_tokens=True)
    final = output_text[output_text.index("### Assistant:")+15:]
    return final

def main():
    user_input = st.text_area("Enter your text here:", height=300)

    if user_input:
        output = maker(user_input)
        st.subheader("Output:")
        st.text_area("Result:", output, height=200)
    else:
        st.info("Please enter some text to generate output.")

if __name__ == "__main__":
    main()
```

## Contributing

Contributions to the Solana Smart Contract Auditor LLM project are welcome. Please feel free to fork the repository, make changes, and submit pull requests.
