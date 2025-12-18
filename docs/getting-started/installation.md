# Installation

This guide will help you install Spatial Reasoners on your system.

## Requirements

- **Python**: 3.11+ (Recommended: 3.13)
- **PyTorch**: 1.13+
- **PyTorch Lightning**: 2.0+

## Quick Install (Recommended)

Install Spatial Reasoners directly from PyPI:

```bash
pip install spatialreasoners
```

This is the easiest way to get started and includes all required dependencies.

## Development Install

For development or to use the latest features from the repository:

```bash
git clone https://github.com/spatialreasoners/spatialreasoners.git
cd spatialreasoners
pip install -e .
```

The `-e` flag installs the package in "editable" mode, so changes to the source code are immediately reflected.

### Development Dependencies

If you're planning to contribute or need development tools:

```bash
pip install -e ".[dev]"
```

This includes additional dependencies for testing, linting, and documentation.

## Verification

Verify your installation by running:

```python
import spatialreasoners as sr
print(sr.__version__)
```

## Optional: Download Datasets & Checkpoints

### Datasets

We provide datasets from the original SRM project, mapped to the Spatial Reasoners framework. You can find them [here](https://github.com/spatialreasoners/spatialreasoners/releases/tag/v1.0.3). Remember to unzip the datasets.zip file and update the relevant paths in your dataset and experiment configs.


For FFHQ-based datasets, you'll need to download [FFHQ](https://github.com/NVlabs/ffhq-dataset) separately and update the path in your dataset config.

### Pretrained Models

Original SRM checkpoints are not directly compatible with Spatial Reasoners due to the changes in framework architecture. However, the checkpoints mapped to the current Spatial Reasoners framework can be found [here](https://github.com/spatialreasoners/spatialreasoners/releases/tag/v1.0.3). Remember to unzip the checkpoints.zip file and point to the right checkpoint `.pt` file in your experiment config.

## Troubleshooting

### Common Issues

**PyTorch Installation**

If you encounter PyTorch-related issues, make sure you have the correct PyTorch version installed. Visit [PyTorch's official installation guide](https://pytorch.org/get-started/locally/) for platform-specific instructions.

**Checkpoint not loading**

When loading the the `.pt` model checkpoint file from our checkpoints release, it is sufficient to have the `checkpoint.load` config set. You shouldn't additionally pass the the checkpoint path in the `trainer.test(..., ckpt_path=...)` argument, as there it expects the whole lightning trainer checkpoint, not just the denoising model checkpoint.

The following is the correct way to run inference with our checkpoints:
```python
import spatialreasoners as sr

@sr.config_main(config_path="./configs/experiments", config_name="SRM_mnist_sudoku") # Custom config with your own benchmarks settings
def inference_mnist_sudoku(cfg):
    """Load checkpoint and generate images for mnist_sudoku."""
    sr.set_cfg(cfg)
    
    assert cfg.mode == "test", "This script is only for testing/inference"

    # Create components from the loaded config
    lightning_module = sr.create_lightning_module(cfg)
    data_module = sr.create_data_module(cfg)
    trainer = sr.create_trainer(cfg)
    
    checkpoint_path = cfg.checkpointing.load
    assert checkpoint_path is not None, "Checkpoint path is not set in config"
    
    print("Components created successfully!")
    print(f"Checkpoint: {checkpoint_path}")
    print(f"Output directory: {cfg.output_dir}")
    print()
    
    # Run test to generate images
    print("Starting inference to generate images...")
    trainer.test(lightning_module, datamodule=data_module) # note no ckpt_path argument
    
    print("Inference complete!")
    print(f"Images saved to: {cfg.output_dir}")


if __name__ == "__main__":
    inference_mnist_sudoku()

```

### Getting Help

If you're still having issues:

1. Check the [GitHub Issues](https://github.com/spatialreasoners/spatialreasoners/issues) for known problems
2. Open a new issue with your system details and error messages
3. Contact us at bpogodzi@mpi-inf.mpg.de

## Next Steps

Now that you have Spatial Reasoners installed, continue with the [Quick Tour](quick-tour.md) to learn the basics, or directly check our [Template Project](https://github.com/spatialreasoners/spatialreasoners/tree/main/example_project).