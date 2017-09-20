# Leak report

The output from valgrind led me to believe that  there were instances in which "strip" was called, allocated memory, 
and then never freed that memory. Upon checking the lines that valgrind specified, it became apparent that the 
source of the leak was that the "strip" function was allocating memory to track how many slots were necessary to 
store saved characters. This memory is then brought into the "is clean" method where it is used for a comparison (line 68)
and then forgotten about, needlessly eating up memory. In order to fix the leak, the variable cleaned must be freed.

Because there are cases where the result of strip would be the empty string, (see line 36), a conditional is needed to see if
the variable cleaned is indeed pointing to memory that needs to be freed. If this is found to be the case, the program now
frees the memory of cleaned after its last use. Upon recompiling and rerunning valgrind, we can see that "All heap blocks
were freed." confirming that the leak has been plugged.


