# javatask-getextension-from-filename
IBM Sterling B2B Integrator Java Task to get extension from filename

The Java Task Service is a Service in the IBM Sterling B2B Integrator Product. It allows to run a piece of code inside a Business Process. The sourcecode of this logic does resemble real Java Code. But to make it work inside a single BP call there are some points to take care of:

Definition of Classes or Methods inside the Service is not possible
Import the required Java Classes
Return an "OK" String
What are the different actions in the Java Task Service
It is possible to read a String Value from Process Data
It is possible to read a XML Node from Process Data
It is possible to write a String Value to Process Data
It is possible to write a XML Node to Process Data
It is possible to read and write the Primary Document
It is possible to call a Custom Java Class
So how could an example BP look like that has a call to the Java Task Service in it? Here is an example


<process name="JavaTaskSample">
<sequence>
<operation name="JavaTaskSample">
	<participant name="JavaTask"/>
	<output message="Xout" >
	<assign to="srcLocationMode">inline</assign>
	<assign to="javaSrc">
	import java.util.Random;
	
	Random randomGenerator = new Random();
	int randomInt = randomGenerator.nextInt(6)+1;
	String s = Integer.toString(randomInt);
	wfc.addWFContent("Random",s); 
	return "OK";
	</assign>
	<assign to="." from="*"></assign>
	</output>
	<input message="Xin" >
	<assign to="." from="*"></assign>
	</input>
</operation>
</sequence>
</process>
This small example is loading a Java Class which is already existing in the JVM: Random. It is called to get a random number. See that the Class is imported first. Also see that the import command has the same scope as the code. There are no brackets to start the Java Method. The resulting random number is then converted to a String and added to Process Data. The Name of the Parameter in the XML Process Data is "Random" At the end a "return" is necessary to terminate the task.

Some other examples of what can be done in a Java Task Service call:

Read a String Value from Process Data.

    String numberOne = (String)wfc.getWFContent("InputNumberValueOne");
Read a XML Node from Process Data. Do not forget to import Classes required for Node

    import org.w3c.dom.Node;
    import org.w3c.dom.NodeList;
    import org.w3c.dom.Element;
    Node node = (Node) wfc.getWFContent("nodeOne");
Write a XML Node to Process Data. Do not forget to import Classes required for Node

    import org.w3c.dom.Node;
    import org.w3c.dom.NodeList;
    import org.w3c.dom.Element;
    Node node = [here is your code]
    wfc.addWFContent("ResultsNode",node);
Read the Primary Document

    import java.io.InputStream;
    import java.io.OutputStream;
    import com.sterlingcommerce.woodstock.workflow.Document;
    Document doc = wfc.getPrimaryDocument();
    String bodyName = doc.getBodyName();
    InputStream is = doc.getBodyInputStream();
    [read from the stream]
    is.close();
    
Create a new Primary Document and write to it. Do NOT forget to flush and close the stream!

    import java.io.InputStream;
    import java.io.OutputStream;
    import com.sterlingcommerce.woodstock.workflow.Document;
    Document newDocument = new Document();
	OutputStream os = newDocument.getOutputStream();
    [write to stream]
    os.flush();
    os.close();
    wfc.putPrimaryDocument(newDocument);
    
