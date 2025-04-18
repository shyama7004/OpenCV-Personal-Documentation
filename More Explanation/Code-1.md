```cpp
#include <opencv2/opencv.hpp>
#include <iostream>
```

- This includes the necessary headers for the OpenCV library (`opencv2/opencv.hpp`) and the standard input-output stream library (`iostream`).

```cpp
using namespace cv;
using namespace std;
```

- These lines allow you to use the classes and functions from the `cv` (OpenCV) and `std` (Standard Library) namespaces without having to prefix them with `cv::` and `std::` respectively.

```cpp
int main(int argc, char** argv) {
```

- This is the main function, which is the entry point of a C++ program. It takes two arguments: `argc` (argument count) and `argv` (argument vector, an array of C-style strings).

```cpp
    if (argc < 2) {
        cout << "Usage: ./example <image_path>" << endl;
        return -1;
    }
```

How it works :-

1. **Check the Number of Arguments:**

   ```cpp
   if (argc < 2) {
   ```

   This condition checks if the number of arguments is less than 2. If it is, it means the user has not provided the required argument (the path to the image file).

2. **Print Usage Message:**

   ```cpp
   cout << "Usage: ./example <image_path>" << endl;
   ```

   If the condition is true, this line prints a usage message to the console. This message informs the user how to correctly run the program. In this case, it indicates that the user should provide the image path as a command-line argument.

3. **Return with Error Code:**
   ```cpp
   return -1;
   ```
   This line terminates the program and returns `-1`, indicating an error. The return value can be checked by the operating system or calling process to determine if the program executed successfully or encountered an error.

```cpp
    // Step 1: Declare matrices
    Mat A, C;
```

- Declares two matrix objects, `A` and `C`, of type `cv::Mat` which is a fundamental image container in OpenCV.

```cpp
    // Step 2: Read the image
    A = imread(argv[1], IMREAD_COLOR);
```

- Reads the image from the path specified by the first command-line argument (`argv[1]`) in color mode (`IMREAD_COLOR`), and stores it in matrix `A`.

```cpp
    // Check if the image was loaded successfully
    if (A.empty()) {
        cout << "Error: Could not open or find the image!" << endl;
        return -1;
    }
```

- Checks if the image was successfully loaded by examining if `A` is empty. If it is empty, an error message is printed and the program returns `-1`.

```cpp
    // Step 3: Use the copy constructor
    Mat B(A);
```

- Creates a new matrix `B` using the copy constructor, which makes `B` a copy of `A`.

```cpp
    // Step 4: Use the assignment operator
    C = A;
```

- Assigns the matrix `A` to `C` using the assignment operator, making `C` a copy of `A`.

```cpp
    // Display the images
    imshow("Original Image", A);
    imshow("Copy Constructor Image", B);
    imshow("Assignment Operator Image", C);
```

- Displays three images in separate windows: the original image (`A`), the copy created with the copy constructor (`B`), and the copy created with the assignment operator (`C`).

```cpp
    // Wait until user press a key
    waitKey(0);
```

- Waits indefinitely for a key press before closing the displayed image windows. The `0` argument specifies an infinite wait.

```cpp
    return 0;
}
```

- Returns `0`, indicating that the program executed successfully.
