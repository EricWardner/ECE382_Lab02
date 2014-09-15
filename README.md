ECE382_Lab02
============

##PreLab
####Diagram

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
