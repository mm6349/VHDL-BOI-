testbench นี้เขียนแต่ละกรณีเรียงเป็นบรรทัดไว้ใน process เลย ไม่ใช้ procedure 

```vhdl
-- tb_and_gate.vhd
-- Inline testbench for and_gate
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity tb_and_gate is
end entity tb_and_gate;

architecture sim1 of tb_and_gate is
    signal a, b : std_logic := '0';
    signal y    : std_logic;
begin

    dut : entity work.and_gate(dataflow)
        port map (a => a, b => b, y => y);

    stim : process
    begin
        a <= '0'; b <= '0'; wait for 10 ns;
        assert y = '0' report "FAIL: 0 and 0" severity error;

        a <= '0'; b <= '1'; wait for 10 ns;
        assert y = '0' report "FAIL: 0 and 1" severity error;

        a <= '1'; b <= '0'; wait for 10 ns;
        assert y = '0' report "FAIL: 1 and 0" severity error;

        a <= '1'; b <= '1'; wait for 10 ns;
        assert y = '1' report "FAIL: 1 and 1" severity error;

        report "Simulation finished" severity note;
        wait;
    end process;

end architecture sim1;
```

แต่ละกรณีมี 2 บรรทัด บรรทัดแรกกำหนด `a` กับ `b` แล้วรอ 10 ns ให้ `y` เปลี่ยนตาม บรรทัดที่สองตรวจ `y` ด้วย `assert` ถ้าค่าไม่ตรงจะพิมพ์ข้อความ FAIL ออกมา การ `wait;` บรรทัดสุดท้ายหยุด process ไว้ไม่ให้วนกลับไปเริ่มใหม่

ถ้าอยากได้แบบสั้นกว่านี้อีก ให้ใช้ waveform assignment นอก process ซึ่งได้สัญญาณ 00 → 01 → 10 → 11 ทุก 10 ns เหมือนกัน แต่ไม่มี `assert` ต้องดูผลจาก waveform เอง

```vhdl
    a <= '0', '1' after 20 ns;
    b <= '0', '1' after 10 ns, '0' after 20 ns, '1' after 30 ns;
```

แบบนี้ต้องสั่งรันด้วย `run 40 ns` เพราะไม่มี process มาหยุดซิมูเลชันให้


---
```vhdl
-- tb_and_gate.vhd
-- Inline testbench for and_gate
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity tb_and_gate is
end entity tb_and_gate;

architecture sim2 of tb_and_gate is
    signal a, b : std_logic := '0';
    signal y    : std_logic;
begin

    dut : entity work.and_gate(dataflow)
        port map (a => a, b => b, y => y);

    stim : process
    begin
        a <= '0'; b <= '0'; 
		wait for 10 ns;
        assert y = '0' report "FAIL: 0 and 0" severity error;

        a <= '0'; b <= '1'; 
		wait for 10 ns;
        assert y = '0' report "FAIL: 0 and 1" severity error;

        a <= '1'; b <= '0'; 
		wait for 10 ns;
        assert y = '0' report "FAIL: 1 and 0" severity error;

        a <= '1'; b <= '1'; 
		wait for 10 ns;
        assert y = '1' report "FAIL: 1 and 1" severity error;

        report "Simulation finished" severity note;
        wait;
		
    end process;

end architecture sim2;

```

---
