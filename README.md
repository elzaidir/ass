# Assembly Syntax Synthesiser

This tool can generate an assembler in C based on simple opcode and mnemonics descriptions. 

## License

Please note that this software is licensed under the GPLv3. As the generated files contain code snippets form this software, any file generated by this software is licensed under the GPLv3 license. Anything assembled with the generated assemblers is, however, not affected by the GPLv3 license.


# Getting started

***DISCLAIMER*** *: It has only been tested on Linux. As it requires POSIX and GNU libraries it probably won't compile for MacOS, and certainly won't for Windows. However, the generated code is portable across all three OSs.*

## Build and install

***INFO*** *: The following commands assume you are working in your home directory on a Debian ditribution.*

Install the dependencies first, using `apt` or your prefered package manager.

```bash
sudo apt install bison flex gcc make git xxd
```

Clone the repository.
```bash
cd ~/
git clone https://github.com/bourquenoud/ass.git
```

Build `ass` with make.

```bash
cd ~/ass
make
```

You can also install it, but it is not required.

```bash
cd ~/ass
sudo make install
```

## Building a simple assembler

***INFO*** *: The following commands assume you have installed `ass`.*

Once `ass` has been built you can generate an assembler C project from an example. Create a directory for your assembler project, this will contain the source file of the generated assembler. We then call `ass`.

```bash
cd ~
mkdir myfirstassembler
ass -o myfirstassembler/simple_assembler.c ass/examples/pic16f887.ass
```

We can then compile the generated file. It only requires the standard libraries.

Here we use `gcc` but you can use any standard compliant C compiler (C11 standard or later).

```bash
cd ~/myfirstassembler/
gcc -o simple_assembler simple_assembler.c
```

If everything went well, you now have a `simple_assembler` executable in your project directory.

## Using the assembler

***INFO*** *: The generated assemblers can currently only output Intel HEX, Xilinx COE and VHDL arrays. More format will be added in the near future. You can implement you own output format using the `%output` command*

To compile an assembler file using our newly built assembler use :

```bash
cd ~/myfirstassembler
cp ../ass/examples/pic16f887.asm pic16f887.asm
./simple_assembler -o output.hex pic16f887.asm
```

You can select the output format with the `-f <FORMAT>` option. For a list of available format, use the `-h` option.

# More

## ASS documentation

A syntax summary is available here : <https://github.com/bourquenoud/ass/blob/master/docs/ass_syntax.md>

An API documention is on its way.

## Syntax highlighting

I have made a very simple extension for Visual Studio Code, available here : <https://github.com/bourquenoud/ass-highlighter>

## Known bugs and missing features

 - ASS only accept partial regex, the "|", negative sets, any grouping and look-head aren't supported.
 - ASS won't report if a pattern is fully shadowed by another.
 - ASS memory is never freed anywhere. As it is short-lived it isn't a huge problem, but this should be taken into account.
 - Most parameters are unused, and default to 16 bits width and 64bit address space.
 - If any state machine at any point has more than 1024 states during generation, the generation will silently fail. For comparison, the example "pic16f887.ass" reaches 199 states for the lexer. The limit can be increase, but the memory usage increase quadratically.
 - Generated assemblers output files in 16bits opcode format, associated parameters are ignored.
 - Generated assemblers will log the wrong line/token depending on the message.
 - Generated assemblers can read multiple files, but won't automatically place code sections. It will place the first encountered instruction at the beginning of the address space, so file order is important.

## Future

We can't declare macros for now, this will be a priority after fixing the current bugs and missing features.

To increase the flexibility, I will make it possible to insert code from the ASS file. This programm is heavily inspired by GNU Bison, and the ability to insert custom code the same way it is done in Bison would help a lot. Syntax will change, but you can expect your old code to remain compatible with newer versions of ASS.

The state machines are implemented using **very** large switch case statements. I'll change it to table based state machines to improve code compactness if performances don't suffer too much.

I'm planning on implementing an automatic documentation generator. Doc being quite important, this is something I'll implement in the near future if I find it easy to do.

Being able to generate linkable files could be useful, but as this software targets mostly small processors this isn't a priority.

I want to implement a way to generate an emulator from the opcode descriptions, but this will probably need a lot of work. This may or may not be implemented one day, but don't expect anything.

# License

***LICENSED UNDER GPLv3***

*This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.*

*This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.*

*You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.*
