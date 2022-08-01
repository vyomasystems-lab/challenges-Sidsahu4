
# Design Verification for all Levels

The verification environment is set up using Vyoma's Uptick Pro provided for the hackathon.


## 

![App Screenshot](https://1drv.ms/u/s!AmD-XWibUu0fhwzdXqWMyXImGI5f?e=80mmxM)


## Level1_Design1 Verification Environment

The CoCoTb based Python test is developed as explained. The test drives inputs to the Design Under Test (31x1Multiplexer module here) which takes in 31 1-bit inputs  and output one of the bits among them and gives 5-bit output which is choosen by select bit of 5bits.

The values are assigned to the input port using

dut.inp0.value=0;
dut.inp1.value=0;
.
.
dut.inp12.value=1;
.
dut.inp30.value=0;
dut.sel.value=01100;

The assert statement is used for comparing the mux output to the expected value.

assert dut.out.value == dut.inp12.value , "Adder result is incorrect: {inp12} != {out}, expected value={1}".format(
                     AssertionError: Mux result is incorrect: 1 != 0, expected value=1

                     

## Test Scenario
Case1
Test Inputs: sel=01100, inp12=11, rest all 30 inputs having value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Case2
Test Inputs: sel=11110,  all 31 inputs having value=11, default value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Output mismatches for the above inputs proving that there is a design bug



## Design Bug

Based on the above test input and analysing the design, we see the following
Case1
5'b01101: out = inp12; // case for inp12 is wrong
5'b01101: out = inp13;

Case2
5'b11101: out = inp29;  // case fot inp30 not added
default: out = 0;
    endcase
## Design Fix

Updating the design and re-running the test makes the test pass.

![App Screenshot](https://1drv.ms/u/s!AmD-XWibUu0fhwoCDiGiCnsezDaE)

## Verification Strategy

 Checked the output for each input ranging from 0 to 31, keeping bits different for 
 input value being checked, from others and also keeping default value different from them. This made my verification task easier.
## Level1_Design2 Verification Environment

The CoCoTb based Python test is developed as explained. The test drives inputs to the Design Under Test (seq_detect_1011 module here) which takes in input bit from user and outputs seq_seen as 1 if 1011 sequence is achieved.

The values are assigned to the input port using

dut.inp_bit.value=1
dut.reset.value=0

The assert statement is used for comparing the seq_detector output to the expected value.

assert dut.seq_seen.value == 1 , " Sequence Detection is not catched: {seq_seen} != {1}, expected value={1}".format( AssertionError: Sequence is not detected: 1 != 0, expected value=1
## Test Scenario
Case1
Test Inputs: sel=01100, inp12=11, rest all 30 inputs having value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Case2
Test Inputs: sel=11110,  all 31 inputs having value=11, default value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Output mismatches for the above inputs proving that there is a design bug



## Test Scenario
Case1
Test Inputs: sel=01100, inp12=11, rest all 30 inputs having value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Case2
Test Inputs: sel=11110,  all 31 inputs having value=11, default value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Output mismatches for the above inputs proving that there is a design bug



## Test Scenario
Case1
Test Inputs: sel=01100, inp12=11, rest all 30 inputs having value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Case2
Test Inputs: sel=11110,  all 31 inputs having value=11, default value=00

Expected Output: out=11

Observed Output in the DUT dut.out=00

Output mismatches for the above inputs proving that there is a design bug



## Test Scenario
 Test Inputs: inp_bit=1, reset=0
Expected Output: seq_seen= 1

Observed Output in the DUT dut.out=0

Output mismatches for the above inputs proving that there is a design bug


## Design Bug

Based on the above test input and analysing the design, we see the following 
SEQ_101:
      begin
        if(inp_bit == 1)
          next_state = SEQ_1011;
        else
          next_state = IDLE;       /* next_state=SEQ_10; */
      end
      SEQ_1011:
      begin
        next_state = IDLE;         /* if(inp_bit==1)
                                    next_state=SEQ_1;
                                       else
                                    next_state=SEQ_10;   */
      end

      ![App Screenshot](https://1drv.ms/u/s!AmD-XWibUu0fhw4s7BUZHid49Hh3)
## Design Fix

Updating the design as mentioned in comment in design bug and re-running the test makes the test pass.

## Verification Strategy

The design failed strictly in overlapping sequence detector scenario. Checked it by providing the required input bits in sequence.

## Level2_Design Verification Environment

The CoCoTb based Python test is developed as explained. The bitmanipulation coprocessor will perform the bitwise expression as choosen by input bits.

The values are assigned to the input port using

 dut.RST_N.value = 0
 dut.RST_N.value = 1
 dut.mav_putvalue_src1.value = mav_putvalue_src1
 dut.mav_putvalue_src2.value = mav_putvalue_src2
 dut.mav_putvalue_src3.value = mav_putvalue_src3
 dut.EN_mav_putvalue.value = 1
 dut.mav_putvalue_instr.value = mav_putvalue_instr


The assert statement is used for comparing the bitmanip output to the expected_mav_putvalue.

error_message = f'Value mismatch DUT = {hex(dut_output)} does not match MODEL = {hex(expected_mav_putvalue)}'
    assert dut_output == expected_mav_putvalue, error_message
## Test Scenario
Case1
Test Inputs: RST_1=1 mav_putvalue_src1=1 mav_putvalue_src2=0 mav_putvalue_src3=0 EN_mav_putvalue=1

Output in the DUT dut_output = dut.mav_putvalue.value

Case2
Test Inputs: RST_1=0 mav_putvalue_src1=1 mav_putvalue_src2=0 mav_putvalue_src3=0 EN_mav_putvalue=1

Output in the DUT dut_output = dut.mav_putvalue.value

Output mismatches for the above inputs proving that there is a design bug
## Design Bug

Based on the above test input and analysing the design, we see the following

for res=0xfffffff mav_putvalue!={1,0xfffffff}
## Design Fix

Updating the design and re-running the test makes the test pass.

res=0xfffffff mav_putvalue=0x1fffffff
## Verification Strategy
 
 Wnt through the resources initially given in repository and made the different test cases and figured out the error.
## Level3_Design Verification Environment

The CoCoTb based Python test is developed as explained. The test drives inputs to the Design Under Test (mainCKT module here) which takes in two 8 bit inputs(-128 to 127)(either signed or unsigned) in binary form and output multiplication of them as 15 bit output. 8th bit is representing sign here in inputs.

The values are assigned to the input port using

 dut.x.value = 01010101
 dut.y.value = 00101010


The assert statement is used for comparing the multiplier output to the expected output.

assert dut.p.value == dut.P.value , "Multiplier result is incorrect: {p} != {P}, expected value={000110111110010}".format( AssertionError: Mux result is incorrect: '000110111110010' != '000011101001110', expected value=1


## Test Scenario

Test Inputs: x=01010101, y=00101010

Expected Output: p= 000110111110010

Observed Output in the DUT dut.P=000011101001110

Output mismatches for the above inputs proving that there is a design bug
## Design Bug

 begin
      
      PP= ((P&l)|(Q&A)|(R&B)|(S&C)); // B and C need to be swapped according to design and proper output

    end
    We swapped x and y values and find that both output are not same, but technically it should come same.

    ![App Screenshot](https://1drv.ms/u/s!AmD-XWibUu0fhwe8mt78WfU6mLv2)
## Design Fix

Updating the design and re-running the test makes the test pass.

![App Screenshot](https://1drv.ms/u/s!AmD-XWibUu0fhwheHT28i_um4sWM)
## Verification Strategy

Firstly I gave some initial values to x and y. Then in the second case I swapped it. Surprisingly the first case showed the correct output, but the second one didn't.
It was easy to comprehend that, i had swapped the values, but it shouldn't have affected the multiplier output.
So it was for sure that somewhere in module the conditions have been interchanged and as it is a multimodular code, I checked each module output seperately and found the error.
It came out that the places of B and C( design variables ) have been interchanged unintentionally in a boolean expression.