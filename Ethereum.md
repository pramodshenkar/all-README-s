#Ethreum : 

##1.smart contract :
    program that runs on evm

##2.Diff smart contract vs other programs
    It can't modified However data of that sc can modify

##3.can sc interact with other sc
    yehhh

##4.Can we call sc on web API 
    Nooo.
    why -> due to security

##5.Can sc has lots of data?
    Nooo
    we can store limited data due to gas limit

##6.Other lang
    vyper, LLL

##7.solidity : static Or dynamic
    static.
    means neccesary to define type of variable.
    in php we can directly create variable without type ie $var = 1 this is dynamic typed lang.

##8.solidity : compiles Or interpreted
    compiled.

##9.sol extension :
    .sol

##10.can 1 file * sc is possible
    Yehhh
    but 1-1 is better

##11.Layout of sol
    pragma statement : who tels compiler what solidtiy version we use
    contract contract-name 
    {
        uint var;
        function foo(){

        }
    }

##12.Diff state variable Vs local variables.
    State var : will persist on blockchain
    local var : only in that function scope.
    ```solidity
        contract A {
            //state variables
            uint a;

            function foo(){
                //local variables
                uint b;
            }
        }
    ```

##15: variable visibility in sol.
    private : only function inside that sc can read this
    public  : anyone can read this

##17.default visibilty for state varibles:
    private

##18.are private variable really private?
    Nooo
    private for EVM
    As ethreum blockchain is public. anyone can read data stored on it.
    So anyone can read any data including private variable from blockchain.

##19.As above ans, anyone can see private variable then how should we deal with imp private data
    by 2 ways
        1.Use hashing
        2. Or dont put private data on blockchain.

##20.Most used datatype? & why?
    uint
    address
    string

##21.