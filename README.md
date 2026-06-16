### Two-Button Predictive Text Entry for LoRa Companions
This project attemts to simulate a low-memory predictive text entry system designed for operating LoRa companion devices with only two buttons. One button cycles sequentially forward through a list of alphabet characters, and the other accepts the character. To make typing fast and usable, this system dynamically reorders the alphabet after every keystroke so that the most likely next letters appear right at the front of the list, reducing the number of button presses required to compose a message.
At its core, the project solves a data compression problem for constrained hardware. While a complete lookup table of all possible character combinations would provide perfect predictions, it requires far too much memory for a microcontroller that already has to run LoRa reception in the background. This system uses offline machine learning to compress a massive statistical text corpus into compact mathematical matrices. Theintention is that the microcontroller can performs fast, deterministic mathematical operations on these small matrices to predict character rankings on the fly, ensuring a minimal RAM footprint without sacrificing prediction accuracy.
#### How It Works</b><br>
The system operates within a three-character context window, tracking only the last three characters entered by the user to predict the next logical letter. It does not use recurrent states or rolling summaries, making it stable and predictable.
The architecture is divided into two distinct parts to accommodate hardware limitations:
<br><b>1. Offline Training</b><br>
Before deployment, a Python script processes a large text corpus to count every occurrence of three-character sequences and their following letters. This establishes an uncompressed statistical reference implementation called the oracle, which represents perfect predictive behavior. The system then uses matrix factorization to compress this massive oracle table into a compact latent geometry. It learns a set of small slot embedding matrices for the input positions and a corresponding character embedding matrix for the output alphabet.
<br><b>2. Runtime Inference</b><br>
On the device, the system generates a dynamic context vector by summing up the slot-specific embeddings of the three current input characters. It then calculates a suitability score for every available character in the alphabet by computing the dot product of this context vector and the corresponding target letter vector. Sorting these final scores produces the optimized alphabet ranking presented to the user on the screen.
#### Usage</b><br>
The current implementation simulates both the training and the deployment phases inside a Python environment to validate the ranking representation before generating C++ hardware artifacts.<br>
Ensure your environment has Python installed along with PyTorch, which is utilized during the offline training phase to optimize the matrix factorization layers.<br>
Run the main training script to process your text corpus, build the statistical reference oracle, and train the compressed embedding matrices.
```bash
python embedding_3gram.py
```
