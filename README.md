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

######Initialization
The first part of the program was to initialize the cipher text, the length, the key, the key length, and a pointer to where you would like the result to be stored

```asm
cipherTxt:	.byte	0xf8,0xb7,0x46,0x8c,0xb2,0x46,0xdf,0xac,0x42,0xcb,0xba,0x03,0xc7,0xba,0x5a,0x8c,0xb3,0x46,0xc2,0xb8,0x57,0xc4,0xff,0x4a,0xdf,0xff,0x12,0x9a,0xff,0x41,0xc5,0xab,0x50,0x82,0xff,0x03,0xe5,0xab,0x03,0xc3,0xb1,0x4f,0xd5,0xff,0x40,0xc3,0xb1,0x57,0xcd,0xb6,0x4d,0xdf,0xff,0x4f,0xc9,0xab,0x57,0xc9,0xad,0x50,0x80,0xff,0x53,0xc9,0xad,0x4a,0xc3,0xbb,0x50,0x80,0xff,0x42,0xc2,0xbb,0x03,0xdf,0xaf,0x42,0xcf,0xba,0x50,0x8f
MSG_LEN:	.equ	0x5e
key:		.byte	0xac, 0xdf, 0x23
KEY_LEN:	.equ	0x03

RESULT:		.equ	0x200
```
######Main loop
The main loop initializes the constants into a register for use and manipulaiton by the program, it also calls the decryptMessage subroutine.
```asm
mov.w	#cipherTxt, r6
mov.w	#key, r7
mov.w	#RESULT, r8
mov.w	#MSG_LEN, r9
mov.w	#KEY_LEN, r10
call    #decryptMessage

forever:    jmp     forever
```
######decryptMessage
The decryptMessage subroutine had to parse through the given ciphertext stored in ROM using a pointer (r6) and pass each byte into decryptByte via r12, it also handles a key of varying length in the same manner. After decryptByte has returned, the decrypted byte is stored in RAM. 

The post increment is where the parsing occurs. I though of it like an index in an array.

```asm
decryptMessage:

nextByte	mov.b	@r6+, r12		;put byte of message into r12
			dec.b	r9
			mov.b	@r7+, r13		;put byte of key into r13
			dec.b	r10

			tst		r9
			jz		end
			call	#decryptCharacter

			mov.b	r13, 0(r8)		;store result
			inc.w	r8

			tst		r10
			jnz		nextByte
			mov.w	#KEY_LEN, r10
			mov.w	#key, r7
			jmp		nextByte

end
            ret
```
######decryptByte
Gets r12 (the message byte) and r13 (byte of key) passed into to by reference. It then xor's the message with the key, effectively decrypting it. Then it returns.
```asm
xor.b	r12, r13
ret
```
#####B-Functionality
The B functionality was achieved by looping through the key countinuously untill the end of the cipher text. the key length was stored in r10 and every time a byte of the key was storied, I decremented r10 untill it was at zero, at that point I reset r10 to the key length and r7 to point to the start of the key.

####Testing and Debugging

#####Method
I used the given string as my cipher text and stored those bytes in ROM, I ran the program in debug mode and steped through slowely so I could keep track of my registers and memory. After seeing that the program was beginning in the correct way, I let it run through to the end then I inspected the memory. I copy and pasted the results into an [online ASCII decoder](http://www.paulschou.com/tools/xlate/binary22.php) to get my final string and see if it made sense.

Build attempt 1: (failed) did not have a "#" in front of constants
Build attempt 2: (failed) can't have post increment in src of an instruction
Build attempt 3: (success) program ran and worked with the varying key length and given B-functionality test!

####Conclusions and Lessons learned
It was honestly way easier to just start coding in assembley and stepping through than writing and making a flowchart first. I found with such a simple program I never even referenced my original pseudo code or flowchart. I'm learning to always look for little mistakes in code and syntax before completely changing my program. Overall I though the lab was successfull, It only took 3 tries and some simple mistakes to get the program fully functional
