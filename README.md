# FPGA FIFO-Based Line Buffer for 2D Convolution

This project implements an FPGA-based **line buffer for image-processing and 2D convolution applications** using three FIFO buffers.

Image data is normally stored and transferred sequentially in raster-scan order. However, a 3×3 convolution requires pixels from three neighboring image rows to be available at the same time. To support this access pattern, the design buffers incoming 8-bit image data across three FIFOs and provides the stored row data in parallel.

Each FIFO has an **8-bit data width and a depth of 8**. Once all three FIFOs are filled, the line buffer asserts a `ready` signal to indicate that valid parallel data is available. During reading, the three FIFO outputs are combined into a **24-bit output** that can be supplied to the next stage of an image-filtering or convolution datapath.

## Features

- Three FIFO-based line buffers
- 8-bit input data
- 8-entry FIFO depth
- 24-bit parallel output
- `full` and `empty` FIFO status signals
- `wr_en` and `rd_en` control signals
- `ready` signal for line-buffer output
- Dual-port memory implementation
- RTL simulation and verification

## FIFO Operation

Each FIFO follows standard first-in-first-out behavior.

A write pointer identifies the next location where incoming data will be stored, while a read pointer identifies the next location to be read.

The main control signals are:

- `wr_en` — enables a FIFO write
- `rd_en` — enables a FIFO read
- `full` — indicates that the FIFO cannot accept additional data
- `empty` — indicates that there is no valid data to read

The write and read pointers are incremented after successful write and read operations.

An additional pointer bit is used to distinguish between the FIFO's `full` and `empty` conditions when the lower address bits of the two pointers are equal.

## Line Buffer

The line buffer contains three FIFO instances.

Incoming 8-bit image data is written sequentially into the FIFOs. Data can be stored while the line-buffer `ready` signal is low.

After all three FIFOs become full, `ready` is asserted. The data from the three FIFOs can then be read simultaneously, producing:

```text
3 × 8-bit = 24-bit output
