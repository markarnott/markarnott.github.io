# Trusted Platform Modules and Trusted Computing: Why aren't we there yet. #
March 4, 2020

## Initial Goal ##
A few months ago I set out to discover how to encrypt application secrets with a Trusted Platform Module, TPM.  A TPM is a chip embedded on a systems motherboard that provides a set of cryptographic functions.  The TPM specifications are developed and promoted by the Trusted Computing Group, TCG.

So why should we care about encrypting application secrets?  

Many recent security breaches have been very sophisticated in nature.  It is now common for hacking groups to spend many months executing a staged, multi-pronged attack strategy.  Applications usually store their secrets in files and expect file system access controls to protect them.  These secrets typically include things like private keys and connection strings with database credentials.  A compromised administrator account or an improperly configured file system could give an attacker access to secrets that would allow direct access to a database or the ability to steal data through a man-in-the-middle attack. 

If an application encrypts its secrets that are stored on disk, it adds another layer of defense to frustrate, delay and possibly stop attackers.  Using a password as the base encryption key is a common approach.  But this password must be stored somewhere and so we end up with the password getting stored on the file system.  This does not add much value.  Instead we want to force the attacker to gain access to the systems memory or gain the ability to execute code on the target system before they can access the applications secrets.  If a TPM acts as the root of trust we could accomplish our goal of adding an effective layer of defense.

## Where Are We Now ##
From an initial survey of the facts I thought that using the TPM should not be difficult.  Trusted Platform Modules have been around since the TPM 1.1b specification released in 2003 [[1](#references)].  The TPM has increased in functionality and sophistication with the TPM 1.2 and 2.0 specification releases.  In May 2017 Microsoft required all Windows 10 certified PC computers to include TPM 2.0 chips.

With TPM version 1.2 the TCG specification included the TCG Software Stack, TSS.  This is a set of standardized high level APIs that give application developers access to TPM functions.

So with the ubiquity of TPMs and a software stack with high level functionality, why aren't TPMs in wide spread use by all kinds of applications?  This is answered best by James  Bottomley, who says that TPMs "have been ubiquitous for a while now, but they have a horrifically bad programming experience" [[2](#references)].  

## What Is Holding Us Back ##
There are three issues holding developers back from TPM adoption.  They are virtualization, incompatability and immaturity.  

For something that has been around since 2003, it is hard to understand why it is immature.  The original TPM 2.0 library specifications were first released in March 2013 [[3](#references)]. These did not include the software stack. In A Practical Guide to TPM 2.0, Arthur and Challener describe the different layers in the TSS in detail [[4](#references)]. They do point out that the TSS specifications were not complete when the book went to the publishers in 2015. This specification process has taken a very long time. The specification for the highest layer of the TSS is still in draft. The specifications for the other layers were released as version 1.0 on September 11, 2019.

This immaturity in the software stack was caused by the fact that the specification changed so much between versions 1.2 and 2.0 that the software stack was completely rewritten.  TPM 1.2 and TPM 2.0 are completely incompatible.  An application cannot use the older TSS to integrate with the newer hardware and the newer software will not work with older hardware.  

The final hurdle is virtualization.  Increasingly software is running in virtal environments.  There are no virtual machine hypervisors that allow the guest OS to access the physical TPM.  There are good reasons for this.  The concern is that TPMs were not designed for the scale of operations that will be seen when multiple guest systems access a single physical TPM.  Deconflicting the contentions that will occur is too difficult.  Microsoft Hyper-V and VmWare have solved this problem by implementing virtual TPMs.  Each guest OS will have its own TPM that is a software implementation in the hypervisor.  While this is an acceptable technical solution, there is a significant usability problem.  In all of these systems the virtual TPM is turned off by default and the guest OS cannot enable the virtual TPM.  It requires system administrator access to reconfigure the hypervisor to enable the virtual TPM.

## What Is Ahead ##
Work continues to finalize the entire TSS stack.  As time passes, more hardware with old incompatible TPM 1.2 hardware will retire.  We are not far from a time when a software application can reasonably expect to be deployed on a system with a mature stable TPM.  We are just not there yet.

In the mean time we need the final version of the TSS to be released, and we need software packages that include the complete stack.  We also need more documentation, more code samples and more conference talks that explain what the TPM is and how it works.

### References ###
[1] “Trusted Computing Platform Alliance Main Specification Version 1.1b” Trusted Computing Group, 2003. [https://trustedcomputinggroup.org/wp-content/uploads/TCPA_Main_TCG_Architecture_v1_1b.pdf](ttps://trustedcomputinggroup.org/wp-content/uploads/TCPA_Main_TCG_Architecture_v1_1b.pdf) [Accessed: 25-Mar-2020].

[2] Bottomley, J., 2018. TPM ENABLING THE CRYPTO ECOSYSTEM FOR ENHANCED SECURITY. [Online Video]. Available: [https://youtu.be/n3rWuUS4WbU](https://youtu.be/n3rWuUS4WbU) [Accessed: 3-May-2020].

[3] “TPM 2.0 Library Specification,” Trusted Computing Group, 15-Mar-2013. [Online]. Available: [https://trustedcomputinggroup.org/resource/tpm-library-specification/](https://trustedcomputinggroup.org/resource/tpm-library-specification/.). [Accessed: 25-Mar-2020].

[4] W. Arthur, D. Challener, and K. Goldman, A Practical Guide to TPM 2.0. APress, 2015, p.77.
