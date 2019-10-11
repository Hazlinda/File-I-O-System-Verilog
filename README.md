# File-I-O-System-Verilog

In order to use the tasks $fdisplay, $fmonitor etc., you must first open a file using the system function $fopen. 

# $fopen 
This $fopen requires a file name and a mode. To open a file for writing, the mode is "w". 
$fopen returns an integer value – file descriptor – or 0 if the file could not be opened. 
This may happen if, for example, the disk is full, or there is no privilege to write to the specified file. 
It is a good idea to check the value returned by $fopen.  

The file descriptor is then used as the first argument when calling $fdisplay, $fmonitor etc. The other arguments are exactly the same as for the corresponding tasks ($display, $monitor etc.)  When you have finished writing to the file, you can close the file with the system task $fclose. Files that are not closed with this system task are automatically closed when simulation finishes


        module fileio_write();

            localparam monitor_file = "file_toWrite.txt";

            integer fd;
            reg [3:0] count;
            reg stop;
            localparam EOF = -1;

            initial
              begin
                stop = 0;
                count = 3'b000;
                while (!stop)
                  #10 count = count + 1;
              end

            initial
              begin
                fd = $fopen(monitor_file, "w");
                if ( !fd )
                begin
                  $display("Error: %s cannot be opened.", monitor_file);
                  $finish;
                end
                $timeformat(-9, 1, "ns", 7);
                $fmonitor(fd, "%t : count = %2h", $realtime, count);

                wait (count == 4'hf)
                #1 $fclose( fd );

                stop = 1;
              end

          endmodule
