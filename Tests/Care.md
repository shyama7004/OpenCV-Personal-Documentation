### Steps to Fix Bazel Workspace Issue for Future Reference:

1. **Navigate to the Project Folder**:

   - Go to the folder that contains your project, in this case, `CPP-TEMPLATE`.

   ```bash
   cd /path/to/CPP-TEMPLATE
   ```

2. **Create a WORKSPACE File**:

   - In the project root, create a `WORKSPACE` file if it doesn't already exist.

   ```bash
   touch WORKSPACE
   ```

3. **Run Bazel Command**:

   - After ensuring the `WORKSPACE` file is in place, run the Bazel command to test your code.

   ```bash
   bazel test //cpp-template/tests/gtest_demo:fib_test
   ```

Now, Bazel should recognize the workspace and successfully run the tests!

<img src ="https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/Tests/qw.png">
