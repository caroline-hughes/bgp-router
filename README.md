BGP Router that manages multiple sockets in a simulated network

Partners: Elena Kosowski and Caroline Hughes

High level approach:\
&emsp;&emsp;&emsp;&emsp;We followed the suggested approach outlined in the project description. 
We spent some time on day 0 in office hours getting a better grasp on masking. 
We spent some time figuring out how to read / interpret what is printed by the simulation on the command line. 
Next we focused on getting first two config tests passing. It took us a while understand how to find the 
longest prefix match and tie-breaking. By the due date of the Milestone we were able to get "update", "data", 
and 'dump" messages handled correctly. During the first day of working on the final submission, 
we were able to get tests 1-5 passing. Figuring out how to handle "withdraw" didn't take very long. We had to spend
alot of time figuring out how to do aggregation and disaggregation, though. We went to office hours to get help with
aggregation. 



Challenges:\
The largest obstacles for us were:
- figuring out how netmasks are used
- understanding how to identify numeric adjacendy, and to do it in the simplest/most efficient way
- getting comfortable with conversions between IP address formatted quads of ints to binary and back

Features of our implementation:
- we've abstracted common functionality out for reuse in various areas. 
   - there are helper functions which are generalized for use in multiple areas 
- for getting for numerical adjacency, we use a simple strategy of comparing the lengths of the masking results, which is constant runtime
- for disaggregation, we kept track of which networks were aggregated. If one of those networks was being withdrawn, we
threw out our forwarding table and rebuilt it using our list of saved announcement messages. 

Testing:
- we used the command line to run config tests as we implemented more and more functionality.
- print statements on the command line aided in our ability to see where bugs were occurring.
- when we got stuck, we read the config file itself to understand what behavior is expected, and where our logic was incorrect.