# **EXP5: Design and Simulation of RAM, ROM, and FIFO Memory with Read and Write Operations Using Verilog HDL**

---

## **Aim**
To design, simulate, and verify the functionality of **RAM**, **ROM**, and **FIFO memory** modules with **read and write operations** using Verilog HDL.

---

## **Apparatus Required**
- System with **Vivado Design Suite**
---

## **Theory**

### **1. Random Access Memory (RAM)**
RAM is a volatile memory used to store data temporarily during program execution. It supports both **read** and **write** operations. Data can be accessed randomly using the address lines.  
In Verilog, RAM can be modeled using a `reg` array with write operation controlled by a **write enable (WE)** signal.

### **2. Read Only Memory (ROM)**
ROM is a non-volatile memory where data is permanently stored and can only be **read**, not written. The contents are initialized during declaration using an `initial` block or `$readmemb`/`$readmemh` commands.

### **3. First In First Out (FIFO) Memory**
FIFO is a sequential buffer that stores data such that the **first data written is the first data read**. It uses two pointers — **write pointer** and **read pointer** — to manage data flow. FIFO finds application in data buffering and inter-module communication.

---

## **Program**

### **1. RAM Module**
```verilog
module Ram(
    input clk,rst,en,
    input [7:0]datain,
    input [9:0]address,
    output reg [7:0]dataout
    );
    reg [7:0]mem_1kb_ram[1023:0];
    always@(posedge clk)
        begin
            if(rst)
                dataout <= 8'b0;
            else if(en)
                mem_1kb_ram[address] <= datain;
            else 
                dataout <= mem_1kb_ram[address];        
        end
endmodule
```
### Testbench for RAM
```verilog
module Ram_tb;
    reg clk_t,rst_t,en_t;
    reg [7:0]datain_t;
    reg [9:0]address_t;
    wire [7:0]dataout_t;
    
   Ram dut(.clk(clk_t),.rst(rst_t),.en(en_t),.datain(datain_t),
                                            .address(address_t),.dataout(dataout_t)); 
     initial 
        begin
         clk_t = 1'b0;
         rst_t = 1'b1;
       #100
         rst_t = 1'b0;
         en_t = 1'b1;
         address_t = 10'd800;
         datain_t = 8'd50;
       #100
         address_t = 10'd900;
         datain_t = 8'd60;
       #100
         en_t = 1'b0;
         address_t = 10'd800;
       #100
         address_t = 10'd900;
       end
       always 
        #10 clk_t = ~clk_t;          
endmodule
```
### Simulation Output for RAM
<img width="1919" height="1079" alt="Ram" src="https://github.com/user-attachments/assets/a5f3b635-eb08-4cc3-9658-a5e8f821d2c8" />

### 2. ROM Module
```verilog
/module Rom(input clk,rst,
   input [9:0]address,
   output reg [7:0]dataout
 );
   reg [7:0] Rom[1023:0];
   integer i;
   initial
       begin
           for(i=0;i<1024;i=i+1)
               Rom[i] = $random;
           end
    always@(posedge clk)
       begin
           if(rst)
               dataout <= 8'b0;
           else
               dataout <= Rom[address];
         end
endmodule
```
### Testbench for ROM
```verilog
module Rom_tb;
    reg clk_t,rst_t;
    reg [9:0]address_t;
    wire [7:0]dataout_t;
    
  Rom dut(.clk(clk_t),.rst(rst_t),.address(address_t),.dataout(dataout_t));
    
    initial
       begin
           clk_t = 1'b0;
           rst_t = 1'b1;
        #100
           rst_t = 1'b0;
           address_t = 10'd700;
        #100
           address_t = 10'd800;
        #100
           address_t = 10'd900;
       end
    always
        #10 clk_t = ~clk_t;   
endmodule
```
### Simulation Output for ROM
<img width="1919" height="1079" alt="Rom" src="https://github.com/user-attachments/assets/91d997c3-8be8-4bda-b6fa-f6677d37411b" />


### 3. FIFO Memory Module
```verilog
// 4x8 FIFO Memory with Read and Write Operations
module fifo_4x8 (
    input clk, reset, wr_en, rd_en,
    input [7:0] data_in,
    output reg [7:0] data_out,
    output reg full, empty
);
    reg [7:0] fifo_mem [3:0];
    reg [1:0] wr_ptr, rd_ptr;
    reg [2:0] count;

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            wr_ptr <= 0;
            rd_ptr <= 0;
            count <= 0;
            full <= 0;
            empty <= 1;
        end
        else begin
            // Write operation
            if (wr_en && !full) begin
                fifo_mem[wr_ptr] <= data_in;
                wr_ptr <= wr_ptr + 1;
                count <= count + 1;
         
    end
endmodule
```
### Testbench for FIFO
```verilog
module tb_fifo_4x8;
    reg clk, reset, wr_en, rd_en;
    reg [7:0] data_in;
    wire [7:0] data_out;
    wire full, empty;

    fifo_4x8 uut(clk, reset, wr_en, rd_en, data_in, data_out, full, empty);

    always #5 clk = ~clk;

    initial begin
        clk = 0; reset = 1; wr_en = 0; rd_en = 0; data_in = 8'h00;
        #10 reset = 0;

        // Write data
        wr_en = 1; data_in = 8'h11; #10;
        data_in = 8'h22; #10;
        
endmodule
```
### Simulation Output for FIFO
<img width="1919" height="1079" alt="FIFO" src="https://github.com/user-attachments/assets/7d1e0647-64dc-42c3-b225-1ce1e7ef4fcf" />

### Result

The RAM, ROM, and FIFO memory modules were successfully designed, simulated, and verified using Verilog HDL in Vivado Design Suite.
All read and write operations performed as expected during simulation.
