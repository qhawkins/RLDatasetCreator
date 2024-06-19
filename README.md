## Overview

This project is a synthetic dataset generation program written in C++. It was created to generate optimal RL trajectories with noise, which are then used as training data for decision transformer models in offline reinforcement learning (RL). The inspiration for this project comes from the paper "Decision Transformers." The goal is to leverage the sequence modeling capabilities of transformers in an offline RL context to obtain superior results compared to contemporary methods.

## Features

- **Environment Reset and Step Methods**: These methods reset the state of the environment and simulate steps in time, ensuring the data is accurate and fresh for each episode.
- **Weighted Future Rewards**: Discounts and accumulates future rewards to reflect the impact of taken actions.
- **Optimal and Random Action Selection**: Uses a combination of optimal and random actions to inject noise into the data, helping the model generalize better.
- **Logging**: Creates a dataset and monitors the progress of dataset generation.

## Code Structure

### `rl_dataset_creator.cpp`

This is the main source file containing the implementation of the dataset generation.

#### Environment Class

- **Reset Method**: Resets the state of the environment to initial parameters at the start of each episode.
- **Step Method**: Simulates each step in the environment, checks time constraints, calculates mid prices, and updates state and rewards based on actions.
- **Weighted Future Rewards Method**: Discounts future rewards to reflect the impact of actions taken.
- **Calc Rewards Method**: Calculates discounted future profits, Sharpe ratio, and penalizes large positions.

#### Random Action Selection

- **Random Action Selection Function**: Selects random actions to inject noise, aiding in robust policy learning.

#### Main Function

- **Optimal and Random Action Selection**: Determines whether the action should be optimal or random and calculates rewards accordingly.
- **Logging**: Logs actions and generates CSV files for each day of simulated actions.

### `CMakeLists.txt`

This file configures the build system for the project.

```cmake
cmake_minimum_required(VERSION 3.18 FATAL_ERROR)
project(rl_dataset_creator)

set(CMAKE_PREFIX_PATH "/home/libtorch/")

find_package(Torch REQUIRED)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${TORCH_CXX_FLAGS}")

add_executable(rl_dataset_creator rl_dataset_creator.cpp)
target_link_libraries(rl_dataset_creator "${TORCH_LIBRARIES}")
set_property(TARGET rl_dataset_creator PROPERTY CXX_STANDARD 17)

# The following code block is suggested to be used on Windows.
# According to https://github.com/pytorch/pytorch/issues/25457,
# the DLLs need to be copied to avoid memory errors.
if (MSVC)
  file(GLOB TORCH_DLLS "${TORCH_INSTALL_PREFIX}/lib/*.dll")
  add_custom_command(TARGET rl_dataset_creator
                     POST_BUILD
                     COMMAND ${CMAKE_COMMAND} -E copy_if_different
                     ${TORCH_DLLS}
                     $<TARGET_FILE_DIR:rl_dataset_creator>)
endif (MSVC)
```

## Running the Program

1. **Clone the Repository**:
    ```sh
    git clone <repository_url>
    cd rl_dataset_creator
    ```

2. **Build the Project**:
    ```sh
    mkdir build
    cd build
    cmake ..
    make
    ```

3. **Run the Program**:
    ```sh
    ./rl_dataset_creator
    ```

## Future Improvements

- **Concurrency**: Implementing a thread pool to run the program concurrently, speeding up execution time.
- **Data Expansion**: Utilizing more financial data to train future models.

## Resources

- **Paper**: [Decision Transformers](https://arxiv.org/abs/2106.01345)
