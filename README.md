ECE382_Lab02
============
#####Objectives
The task was to create a "xor" decrypter in assembly to run on an MSP430. The program was required to store an encrypted string byte by byte in ROM then read that "ciphertext" and decrypt it based on a given key then store the decrypted message in RAM. 

see http://en.wikipedia.org/wiki/XOR_cipher

##PreLab
####Diagram
![alt tag](https://raw.githubusercontent.com/EricWardner/ECE382_Lab02/master/lab2flow.png)
####Psuedo-Code
```C
void main(){
    0x0-0x100 (or something) = encrypted bytes;
    0xBEEF = key;
    KeyPtr = 0xBEEF;
    MsgPtr = 0x0;
    RAMPtr = 0x200;

    decryptMsg();
}

void decryptMsg(){

  for(encryptedBytes.length){
    byteOfMsg = @MsgPtr;
    MsgPtr++;
    
    decryptByte(ByteOfMsg);
  }
    jmp end;
}

void decryptByte(byte MsgByte){
  for(key.length){  
    push keyPtr
    inc keyPtr
  }
    xor byteOfMsg, keyPtr;
    mov keyPtr > RAMPtr;
    inc RAMPtr;
  for(key.length){
    pop keyPtr;
    dec keyPtr;
  }
    ret
}
```
####Design Process
After creating a good psuedo-code the design process was fairly straight forward. The hardest part was knowing the right syntax for constants and pointers. 

#####Initialization
The irst part of the program was to initialize the cipher text, the length, the key, the key length, and a pointer to where you would like the result to be stored

```asm
cipherTxt:	.byte	0xf8,0xb7,0x46,0x8c,0xb2,0x46,0xdf,0xac,0x42,0xcb,0xba,0x03,0xc7,0xba,0x5a,0x8c,0xb3,0x46,0xc2,0xb8,0x57,0xc4,0xff,0x4a,0xdf,0xff,0x12,0x9a,0xff,0x41,0xc5,0xab,0x50,0x82,0xff,0x03,0xe5,0xab,0x03,0xc3,0xb1,0x4f,0xd5,0xff,0x40,0xc3,0xb1,0x57,0xcd,0xb6,0x4d,0xdf,0xff,0x4f,0xc9,0xab,0x57,0xc9,0xad,0x50,0x80,0xff,0x53,0xc9,0xad,0x4a,0xc3,0xbb,0x50,0x80,0xff,0x42,0xc2,0xbb,0x03,0xdf,0xaf,0x42,0xcf,0xba,0x50,0x8f
MSG_LEN:	.equ	0x5e
key:		.byte	0xac, 0xdf, 0x23
KEY_LEN:	.equ	0x03

RESULT:		.equ	0x200
```
######Main loop
```asm
 mov.w	    #cipherTxt, r6
            mov.w	#key, r7
            mov.w	#RESULT, r8
			mov.w	#MSG_LEN, r9
			mov.w	#KEY_LEN, r10
            call    #decryptMessage

forever:    jmp     forever
```
