Project-1
=========

Project that takes in objects and places them into a graph. The user can then ask to print the graph or get the shortest distance.

//Below is the main method websearch




public class WebSearch {
	public static void main(String[] args ) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));	// scanner
		ReadFile rf = new ReadFile(args[0]); // reads input file


		System.out.println("Hello and welcome to Websearch");
		while(true){


			System.out.println();
			System.out.println("Choose an option or quit:");
			System.out.println("1. Print the Graph");
			System.out.println("2. Print the Degrees of Separation");
			System.out.println();

			String choice = br.readLine();

			if (choice.contains("quit")){
				return;
			}
			// PART 1 (PRINTING GRAPH)

			if (choice.contains("1")){
				System.out.println();
				System.out.println("Printing Graph...");

				for (int i = 0; i <rf.getNumberOfWebpages(); i++){
					String keyphrases = rf.getKeyPhrases(i).toString();
					keyphrases = keyphrases.substring(1, keyphrases.length()-1);
					String outlinks = rf.getOutLinks(i).toString();
					outlinks = outlinks.substring(1, outlinks.length()-1);
					outlinks = outlinks.trim();
					outlinks = outlinks.replace(" ", "");
					System.out.println(i + " " + "(" +keyphrases+ ")" + ":" + " " + outlinks);
				}

			}




			// Part 2 (DEGREES OF SEPERATION)

			Vertex[] ArrayofVertices = new Vertex[rf.getNumberOfWebpages()]; 
			for (int i = 0; i <ArrayofVertices.length; i++){  
				Vertex newVertex = new Vertex(i, rf.getPageTitle(i), rf.getKeyPhrases(i), null, null, false);
				ArrayofVertices[i]=newVertex; 
			}
			//This above block of code creates an unlinked list of vertexes based on the text file.

			for (int i = 0; i <ArrayofVertices.length; i++){
				Neighbor newneighbor = new Neighbor ();
				ArrayList<Integer> phrases = rf.getOutLinks(i);
				ArrayofVertices[i].neighbors=newneighbor; 
				for (int g=0; g<rf.getNumberOfOutLinks(i);g++){
					Neighbor temporaryneighbor = new Neighbor();
					newneighbor.node=ArrayofVertices[phrases.get(g)];
					if (g==rf.getNumberOfOutLinks(i)-1){
						newneighbor = newneighbor.next;
					}
					else {newneighbor.next= temporaryneighbor;
					newneighbor=newneighbor.next; 
					}  
				}
			}
			//This above block of code takes the unlinked list of vertexes and sets a neighbor class for each of them based on the
			//outlinks of the specific node. 

			if (choice.contains("2")){
				System.out.println("Title of origin Page");
				String start = br.readLine(); 
				int counter=0;
				int deg=0;
				int stop=0;
				int temporary=0;

				while(!start.equals(ArrayofVertices[counter].titleofthepage)){
					counter++;
				}
				Vertex startVertex = ArrayofVertices[counter];				
				counter = 0;
				//Lines 89-93 finds the index of the input and sets the origin to that.

				System.out.println("Title of destination Page");
				String finish = br.readLine(); // takes input and store as destination 
				while(!finish.equals(ArrayofVertices[counter].titleofthepage)){
					counter++;
				}
				Vertex endVertex = ArrayofVertices[counter];	
				//Lines98-101 does the same as the above comment but for the destination.

				int result=0;
				Queue<Vertex> queue = new LinkedList<Vertex>();
				Queue<Integer> countlist = new LinkedList<Integer>();
				//Each of these creates a queue of their respective type.

				queue.add(startVertex);
				//Adds the origin to the vertex queue.


				if (start == finish)
					result=0;
				//If the origin and destination are the same, return 0.
				else if (start!=finish){
					while (!queue.isEmpty()){
						Vertex temp;
						temp=queue.remove();
						Neighbor temp2= new Neighbor();
						temp2=temp.neighbors;
						//As long as the queue isn't empty, lines 118-121 will pop the queue, store it in variables, and then save the temp's 
						//Neighbors and begin checking neighbors below.
						while (temp2!=null){
							if (endVertex == temp2.node){
								stop=1;
								//checks if a neighbor is the destination.
							}
							if (temp2.node.visited==false){
								temp2.node.visited=true;
								queue.add(temp2.node);
								deg++;
								//checks to see if a neighbor has been visited before, if not, it visits it 
							}
							temp2=temp2.next;
						}
						countlist.add(deg);
						//adds the current deg of sep to the countlist queue.
						if (stop==1)
							break;
						
						deg=0;
					}
					temporary=countlist.remove();
					//Pops the deg of sep from the count list queue and saves it as temporary
			
					result=breadthfirstsearch(countlist,temporary);
				}
				if (stop==0)
					result=-1;
				//if theres no possible path, return -1.
				for (int i=0;i<rf.getNumberOfWebpages();i++)
					ArrayofVertices[i].visited=false;
				//resets the linked list for use again
	
				System.out.println("Degrees of separation between pages "+start+" and "+finish+": "+result);
			}

		}
	}
	static public int breadthfirstsearch(Queue<Integer> thru,int X){

		int recur=0;
		int sum=0;
		int pop=0;
		int wait=0;

		for(int i=0;i<X;i++){
			if (!thru.isEmpty()){
				pop=thru.remove();
				if (pop==0)
					wait++;
				else
					recur=pop+recur;
			}
		}
		if (wait-recur>=0)
			sum=1;
		else
			sum=1+breadthfirstsearch(thru,recur);
		return sum;
	}
}

//Below is the class Vertex

public class Vertex {
    
	public static 
	Neighbor Neighbor;
	int index;
	String titleofthepage;
	ArrayList <String> keyphrases;
	Neighbor neighbors;
	Vertex nextNode;
	boolean visited;
    
	public Vertex(String name, int id, ArrayList<String> phrase, Neighbor neighbor, Vertex next, boolean visited){

		this.titleofthepage = name;
		this.index = id;
		this.keyphrases = phrase;
		this.neighbors = neighbor;
		this.nextNode = next;
		this.visited = visited;
        
        
	}
    
	public Vertex(int id, String name, ArrayList<String> phrase, Object Neighbor, Object next, boolean visited)
	{
		
		this.titleofthepage = name;
		this.index = id;
		this.keyphrases = phrase;
		neighbors = null;
		nextNode = null;
		this.visited = visited;
	}
	
	
	
}
//below is the class neighbor


public class Neighbor {
	Vertex node;
	Neighbor next;
	}

