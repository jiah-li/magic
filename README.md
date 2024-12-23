<div align="center">
    <h1>
    Exploiting the Index Gradients for Optimization-Based Jailbreaking on Large Language Models <br><br>
     <a href="https://arxiv.org/abs/2412.08615"> <img alt="paper link" src="https://img.shields.io/badge/Paper-arXiv-red"> </a>
     <a href="data/advbench/harmful_behaviors.csv"> <img alt="data link" src="https://img.shields.io/badge/Data-InstructMT-blue"> </a> 
    </h1>
</div>

This code repository is the implementation of our paper: [Exploiting the Index Gradients for Optimization-Based Jailbreaking on Large Language Models](https://arxiv.org/abs/2412.08615), accepted by COLING 2025.

## MAGIC

We investigate the process of GCG and identify an issue of **Indirect Effect**, the key bottleneck of the GCG optimization. And we propose the **M**odel **A**ttack **G**radient **I**ndex G**C**G (**MAGIC**).

### Motivation

We investigate the **Indirect Effect** between the gradient values of current suffixes and the updated token indexes, which demonstrates that replacing tokens with negative gradient values fails to effectively reduce adversarial loss. We carry out this study in 1000 iterations of the naive GCG algorithm.

<div align="center">
    <img src="./assets/heatmap.jpg" width="50%" alt="heatmap">
    <p>Fig.1: The heatmap reflects the changes of the current gradient values.</p>
</div>

### Method

 The GCG concatenates harmful instruction and adversarial suffix inducing Target LLM to produce harmful content. The MAGIC improves the optimization process of the adversarial suffix. The **Gradient-based Index Selection** investigates the One-Hot vectors corresponding to suffixes and only selects index tokens with positive gradient values. **Adaptive Mutil-Coordinate Update** selects multiple tokens from the previously determined index range for updating, achieving jailbreaking of LLMs.

<div align="center">
    <img src="./assets/method.jpg" width="80%" alt="method">
    <p>Fig.2: An illustration of our approach MAGIC.</p>
</div>

## Start

Using conda to create an environment for MAGIC:

```bash
conda create -n magic python=3.10.1
conda activate magic
```

Run the following command to install dependencies:

```bash
pip install -e .
```

Downloading [Vicuna-7B](https://huggingface.co/lmsys/vicuna-7b-v1.3) or/and [LLaMA-2-7B-Chat](https://huggingface.co/meta-llama/Llama-2-7b-hf) firstly.  

Before you begin, please modify your model path in `experiments/configs/individual_xxx.py`(for individual experiments) and `experiments/configs/transfer_xxx.py` (for multiple behaviors or transfer experiments) first.

```python
    config.model_paths = [
        "/DIR/vicuna/vicuna-7b-v1.3",
        ... # other models
    ]
    config.tokenizer_paths = [
        "/DIR/vicuna/vicuna-7b-v1.3",
        ... # other tokenizers
    ]
```

## Experiments 

In order to facilitate comparison with the baseline, our repository has followed the code base of the original [GCG](https://github.com/llm-attacks/llm-attacks/blob/main/README.md?plain=1). The `experiments` contain code to reproduce our MAGIC experiments on AdvBench.

- To run individual experiments with harmful behaviors and harmful strings (i.e. 1 behavior, 1 model or 1 string, 1 model), run the following code inside `experiments` (changing `vicuna` to `llama2` and changing `behaviors` to `strings` will switch to different experiment setups):

```bash
cd launch_scripts
bash run_gcg_individual.sh vicuna behaviors
```

- To perform multiple behaviors experiments (i.e. 25 behaviors, 1 model), run the following code inside `experiments`:

```bash
cd launch_scripts
bash run_gcg_multiple.sh vicuna # or llama2
```

- To perform transfer experiments (i.e. 25 behaviors, 2 models), run the following code inside `experiments`:

```bash
cd launch_scripts
bash run_gcg_transfer.sh vicuna 2 # or vicuna_guanaco 4
```

## Example

We provide a case shows that the suffix optimizated by our MAGIC, successfully jailbreak GPT-4, eliciting harmful responses.

<div align="center">
    <img src="./assets/example.jpg" width="90%" alt="example">
    <p>Fig.3: Example by MAGIC and response from GPT-4.</p>
</div>

## Citation

If you find this useful in your research, please consider citing:
```
@misc{li2024exploitingindexgradientsoptimizationbased,
      title={Exploiting the Index Gradients for Optimization-Based Jailbreaking on Large Language Models}, 
      author={Jiahui Li and Yongchang Hao and Haoyu Xu and Xing Wang and Yu Hong},
      year={2024},
      eprint={2412.08615},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2412.08615}, 
}
```

## License

```MAGIC``` is licensed under the terms of the MIT license. See [LICENSE](LICENSE) for more details.
