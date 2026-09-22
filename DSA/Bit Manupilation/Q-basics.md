- how to add two number without using third variable 

a = a ^ b;
b = a ^ b;
a = a ^ b;


- how to add two numbers without using + operator

function add(a, b) {
    while (b !== 0) {
        let carry = a & b;
        a = a ^ b;
        b = carry << 1;
    }

    return a;
}

- check if ith bit is set or not 

if((N>>i)&1==0){
    return false
}
else{
    true
}

- set the ith bit 


N | (1<<i) 


- Clear the ith bit 

N & ~(1<<i)               

- Toggle the ith bit 

N ^ (i<<i)

