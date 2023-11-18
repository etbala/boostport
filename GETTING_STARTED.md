Public SSH Key:

To connect to our z/OS system, we require your public SSH key. If you are unsure how to generate and retrieve your public SSH key, please follow the steps outlined below:

Open your terminal or command prompt.

Type the command "ssh-keygen -t rsa" and press Enter. This will generate a new SSH key pair.

Follow the prompts to specify the file name and location for saving the key pair.

Once the key pair is generated, locate the public key file (typically named "id_rsa.pub") and provide it to me.

Github also has a doc on how to do this here: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent


Next, SSH into the system. This can be done with the ID provided in response to the above. 

```ssh YourUsername@IDProvided```

The username you should have created and the ID is a sequence of numbers in the form 123.123.123.12
