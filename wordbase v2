import java.util.*;
import java.io.*;

/*
Steps for getting point Board:
change the board: 0 yours, 1 white, 2 opponents, 3 bombs
*/

public class WordBase 
{
	public static char[][] board = new char[15][12]; //gameboard with letters
	public static int[][] point = new int[15][12]; //gameboard that says who owns what tile
	public static String[] dict = new String[172821]; //dictionary
	public static String team; //orange or blue
	public static int height; //how big you want your list to be
	public static String coord; //boolean for asking whether or not you want to do tile search
	public static int yCoord; //coordinate for tile search
	public static int xCoord; //coordinate for tile search
	public static int oriYours; //how much you initially own
	public static int oriTheirs; //how much opponent initially owns
	public static ArrayList<String> answer = new ArrayList<String>(); //stores answers
	public static ArrayList<Integer> lengths = new ArrayList<Integer>(); //stores answers' lengths
	public static ArrayList<Integer> yPos = new ArrayList<Integer>(); //stores initial position of answers
	public static ArrayList<Integer> xPos = new ArrayList<Integer>(); //stores initial position of answers
	public static ArrayList<Integer> yNew = new ArrayList<Integer>(); //stores final y position of answers
	public static ArrayList<Boolean> tile = new ArrayList<Boolean>(); //for tile search, stores whether or not the specified tile is in the answer
	public static ArrayList<Integer[][]> newBoards = new ArrayList<Integer[][]>();
	public static ArrayList<Integer> gained = new ArrayList<Integer>(); //board control gained, not yet added
	public static ArrayList<Integer> cutoff = new ArrayList<Integer>(); //board control that opponent lost, not added
	
	public static void main(String[] args) throws IOException
	{	
		Scanner sc = new Scanner(System.in);
		System.out.println("Notes: Coordinates are in (y, x), where y goes down and x goes right.");
		System.out.print("Load Game: "); //asks for your game's name (you label it in an input file)
		String game = sc.nextLine();
		
		System.out.print("Target specific tile? (Y/N): "); //choose whether or not you want to do a tile search (ie. look for words that hit a certain tile
		coord = sc.nextLine();
		if (coord.equals("Y")) //asking for coordinates
		{
			System.out.print("Y Value: ");
			yCoord = sc.nextInt();
			System.out.print("X Value: ");
			xCoord = sc.nextInt();
		}
		System.out.print("Size of each list?: "); //choose # of words in each list
		height = sc.nextInt();
		System.out.println();
		
		//find the correct game in the input file and set the team
		BufferedReader f = new BufferedReader(new FileReader("WordBase.in"));
		String test1 = "";
		while (!test1.startsWith(game)) //go through all the file lines until you hit the inputted save
		{ 
			test1 = f.readLine();
		}
		StringTokenizer st1 = new StringTokenizer(test1);
		String blah = st1.nextToken();
		team = st1.nextToken();
		
		//input the dictionary
		BufferedReader d = new BufferedReader(new FileReader("fulllist.txt"));
		for (int i = 0; i < dict.length; i++)
		{
			dict[i] = d.readLine();
		}
		
		//input the gameboard into an array using WordBase.in; there's a border around the regular gameboard
		for (int i = 0; i < 12; i++) // the ~ fill ins serve no purpose, ignore them
		{
			board[0][i] = '~'; 
			board[14][i] = '~';
		}
		for (int i = 0; i < 15; i++)
		{
			board[i][0] = '~';
			board[i][11] = '~';
		}
		for (int i = 1; i < 14; i++) //fill in the rest with the characters from the input file
		{
			StringTokenizer st = new StringTokenizer(f.readLine());
			for (int j = 1; j < 11; j++)
			{
				board[i][j] = st.nextToken().charAt(0);
			}
		}
		
		//input the gameboard point status into an array using WordBase.in (who owns what)
		f.readLine();
		for (int i = 0; i < 12; i++) //these border -1's are needed to detect when an edge has been reached
		{
			point[0][i] = -1;
			point[14][i] = -1;
		}
		for (int i = 0; i < 15; i++)
		{
			point[i][0] = -1;
			point[i][11] = -1;
		}
		for (int i = 1; i < 14; i++) //fill in the rest with your input board
		{
			StringTokenizer st = new StringTokenizer(f.readLine());
			for (int j = 1; j < 11; j++)
			{
				point[i][j] = Integer.parseInt(st.nextToken());
			}
		}
		
		//set the original team scores: how much you own and how much opponent owns
		for (int i = 1; i < 14; i++)
		{
			for (int j = 1; i < 11; i++)
			{
				if (board[i][j] == 0)
				{
					oriYours++;
				}
				if (board[i][j] == 2)
				{
					oriTheirs++;
				}
			}
		}
		
		//start going through the possible starting moves and testing them for possible words
		for (int i = 1; i < 14; i++) //y cord
		{
			for (int j = 1; j < 11; j++) //x cord
			{
				if (point[i][j] == 0) //only goes through tiles you own
				{ 
					String word = "" + board[i][j]; //creates the word to be tested (ie. adding the new letter to the initial word)
					int[][] point1 = new int[15][]; //clones a new array to change the tile in question to one that's been used already (-1)
					for (int k = 0; k < 15; k++)
					{
						point1[k] = point[k].clone();
					}
					point1[i][j] = -1;
					ArrayList<String> list = new ArrayList<String>(); //creates an empty list that we'll later fill with possible words
					for (int k = 0; k < dict.length; k++)
					{
						if (dict[k].startsWith(word)) //only words that start with the given letter go in the new arraylist
						{
							list.add(dict[k]);
						}
					}
					int y1 = i; //the initial y coordinate
					int x1 = j; //initial x coordinate
					boolean cCheck; //intermediary variable that i use to tranfer whether or not the user wants a tile search done
					if (coord.equals("Y"))
					{
						cCheck = false; //words are only added when cCheck is true - ie. when the tile has been reached
					}
					else
					{
						cCheck = true; //automatically add all words to cCheck to true if tile search is not selected. doesn't actually do anything
					}
					int high; //what if a word does a u-turn before finishing? high makes sure the results include the highest place reached
					if (team.equals("O")) //orange wants to go down, so start from 0
					{
						high = 0;
					}
					else //blue wants to go up, so start from 15
					{
						high = 15;
					}
					test(i, j, word, point1, list, y1, x1, cCheck, high); //into the method we go
				}
			}
		}
		
		
		//sort, choose, and output the answers
		if (coord.equals("Y")) //prints the words that pass the tile search only if you chose to do a tile search
		{
			System.out.println("Words that pass through specified tile. (Coordinate, Word, Maximum Y Value)");
			for (int i = 0; i < answer.size(); i++)
			{
				if (tile.get(i) == true) //ie. when the tile wanted is in the string combination
				{
					System.out.println("(" + yPos.get(i) + ", " + xPos.get(i) + ") " + answer.get(i) + " (" + yNew.get(i) + ")");
				}
			}
			System.out.println();
		}
		System.out.println("Top " + height + " longest words. (Coordinate, Word, Length, Maximum Y Value)"); //longest words
		for (int i = 0; i < height; i++) //remember height is how many words per list. 
		{
			int ans = 0;
			int target = 0;
			for (int j = 0; j < answer.size(); j++) //go through the whole list of answers...
			{
				if (lengths.get(j) >= ans) //whenever a longer word is encountered we switch to that as the new "champion"
				{
					ans = lengths.get(j); //grab the length
					target = j; //grab the index of the length
				}
			}
			//by now we've gone through all the answers and picked the longest word
			System.out.println("(" + yPos.get(target) + ", " + xPos.get(target) + ") " + answer.get(target) + " (" + lengths.get(target) + ") (" + yNew.get(target) + ")");
			lengths.set(target, 0); //to prevent this word from being picked again, we set its length to 0 - useless
		}
		System.out.println();
		
		System.out.println("Closest " + height + " words to the opponent's side. (Coordinates, Word, Maximum Y Value)"); //words closet to enemy base
		for (int i = 0; i < height; i++)
		{
			int ans;
			int target = 0;
			if (team.equals("O")) //if you're orange, you're moving down and want a HIGH y value (since java y axis goes up to down)
			{
				ans = 0;
				for (int j = 0; j < answer.size(); j++) //go through them all...
				{
					if (yNew.get(j) >= ans) //if one y value is seem to be higher then it sets it as the new "champion"
					{
						ans = yNew.get(j); //value
						target = j; //index of value
					}
				}
			}
			else //or if you're blue team, you want a LOW score
			{
				ans = 14;
				for (int j = 0; j < answer.size(); j++)
				{
					if (yNew.get(j) <= ans) //difference is that now it must be smaller to be crowned the champion
					{
						ans = yNew.get(j);
						target = j;
					}
				}
			}
			System.out.println("(" + yPos.get(target) + ", " + xPos.get(target) + ") " + answer.get(target) + " (" + yNew.get(target) + ")");
			if (team.equals("O"))
			{
				yNew.set(target, -1); //orange, so we set the victor as an impossibly low score
			}
			else
			{
				yNew.set(target, 15); //blue, so we set the victor as an impossibly high score
			}
		}
		
		//calculate and output the three values
		
	}
	
	public static void test(int y, int x, String word, int[][] point1, ArrayList<String> list, int y1, int x1, boolean cCheck, int high)
	{
		for (int i = y - 1; i <= y + 1; i++) //surrounding 3, y
		{
			for (int j = x - 1; j <= x + 1; j++) //surrounding 3, x
			{
				if (point1[i][j] != -1) //cannot be a tile used before or out of bounds
				{
					String word1 = word + board[i][j]; //create the new word
					int high1 = high; //don't want to affect the other word combos made, so create a new high1 within scope
					if (team.equals("O")) //orange means it can only go up
					{
						if (i > high1)
						{
							high1 = i;
						}
					}
					else //blue means it can only go down
					{
						if (i < high1)
						{
							high1 = i;
						}
					}
					int[][] point2 = new int[15][]; //clones a new array to change the next tile to one that you own (-1)
					for (int k = 0; k < 15; k++)
					{
						point2[k] = point1[k].clone();
					}
					point2[i][j] = -1; //changes the tile to one used already
					ArrayList<String> list1 = new ArrayList<String>(); //new blank list
					boolean cCheck1 = cCheck; //copies the original cCheck so that if it was already true, it'll stay true
					if (yCoord == i && xCoord == j) //checks whether the coord asked for has been reached here with a marker
					{
						cCheck1 = true;
					}
					for (int k = 0; k < list.size(); k++) //now, for every word in the original list...
					{
						if (list.get(k).startsWith(word1)) //if it starts with the combo thus far..
						{
							if (list.get(k).equals(word1)) //and is also the whole word..
							{
								if (cCheck1) //if this word has passed through the wanted tile...
								{
									tile.add(true); 
								}
								else
								{
									tile.add(false); //if there's no tile search selected, all words will end up being true
								}
								answer.add(list.get(k)); //look at the global variables for what all these arraylists do
								lengths.add(list.get(k).length());
								yPos.add(y1);
								xPos.add(x1);
								yNew.add(high1);
								/*//clone point2 and change all the -1 to 0 again (since you're done)
								//then save it two newBoards
								int[][] point3 = new int[15][]; //clones a new array to change the next tile to one that you own (-1)
								for (int l = 0; l < 15; l++)
								{
									point3[k] = point2[k].clone();
								}
								for (int l = 1; l < 14; l++)
								{
									for (int m = 1; m < 11; m++)
									{
										if (point3[l][m] == -1)
										{
											point3[l][m] = 0; //now all of your tiles are labelled
										}
									}
								}*/
							}
							
							
							else //means it starts with the string in question but isn't a word - yet
							{
								list1.add(list.get(k)); //add it to the new list
							}
						}
					}
					if (list1.size() > 0) //we only need to do the test if there are words in the new list
					{
						test(i, j, word1, point2, list1, y1, x1, cCheck1, high1);
						//^with this we start the test anew with the surrounding characters, which in turn will do the test with their surroundings..etc.
					}
				}
			}
		}
	}
}
