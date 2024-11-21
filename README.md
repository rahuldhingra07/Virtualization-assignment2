
Questions - 

1. Describe in detail the steps you used to complete the assignment. Consider your reader to be someone skilled in software development but otherwise unfamiliar with the assignment. Good answers to this question will be recipes that someone can follow to reproduce your development steps. Note: I may decide to follow these instructions for random assignments, so you should make sure they are accurate.
solution - 
Forking and Cloning the Kernel Repository: I started by forking the official Linux kernel repository from GitHub. After that, I cloned my forked repository into my outer VM to get a copy of the kernel source on my local machine.

Configuring the Kernel: Once the repository was cloned, I ran the kernel configuration tool (using make menuconfig) to ensure that KVM support was enabled. This configuration step is essential to ensure the kernel is prepared for virtualization.

Building the Kernel: With the configuration in place, I compiled the kernel using the make command. Since kernel compilation can take a long time, I used make -j$(nproc) to speed up the process by utilizing all available CPU cores.

Testing the Kernel Build: After building the kernel, I took a snapshot of the VM, so I could restore it if there were any issues with the newly built kernel. I then installed the new kernel and rebooted the VM to check if everything was working as expected.

Modifying KVM Source Code: The next step involved modifying the KVM source code to track the different exit types. I added counters for each exit type and set up the system to print the counts after every 10,000 exits. This step required a deep dive into the KVM exit handler code to ensure I was correctly tracking the different exit events.

Rebuilding and Installing the Modified Kernel: After modifying the kernel source, I rebuilt it, installed the new modules, and updated the bootloader. After rebooting the VM, I tested the changes by checking the kernel logs to verify the exit counts were being printed correctly.

Finalizing the Submission: Once I confirmed the changes were working as expected, I committed the changes to my GitHub repository. I also wrote up a README file detailing the steps I took and submitted the repository.

2. Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there
more exits performed during certain VM operations? Approximately how many exits does a full VM
boot entail?
solution - 
Exits during VM Operations: The number of exits does not increase at a stable rate. Instead, there are bursts of exits during certain VM operations. For example:
Context Switches: These occur frequently when the hypervisor switches between the guest and host.
Interrupt Handling: More exits occur during I/O operations or hardware interrupts.
System Calls: These will trigger additional exits when the guest makes a system call that requires the hypervisor's intervention.
Exits During Full VM Boot: A full VM boot will typically involve several thousand exits. This includes:
Kernel initialization, setting up hardware drivers, loading the root filesystem, and setting up networking and other resources.
As an estimate, a typical boot process for a guest VM may involve anywhere from 3,000 to 10,000 exits, depending on the complexity of the boot process.


3. Of the exit types, which are the most frequent? Least?
solution - 
Most Frequent Exit Types:
RDTSC (Read Time-Stamp Counter) exits are the most common, as they happen frequently during timekeeping and performance monitoring operations.
IRQ Exits (Interrupt Request Exits) are also frequent, particularly during I/O operations, where the guest needs to handle interrupts from hardware devices.
Least Frequent Exit Types:
Machine Check Exceptions (MCE): These are very rare and only occur when there’s a hardware error.
Hypercall Exits: These occur when the guest calls the hypervisor to perform operations such as memory management or VM control operations. These are less common than other exit types.

