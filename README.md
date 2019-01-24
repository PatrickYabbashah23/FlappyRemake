# FlappyRemake
#This program is a simulation of the game "Flappy Bird" but instead moves downward; in addition to the speed of the game play is faster

package FlappyBird;

import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Rectangle;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;
import java.util.ArrayList;
import java.util.Random;

import Audio.AudioPlayer;

import javax.swing.Timer;

import javax.swing.JFrame;

public class FlappyBird implements ActionListener, MouseListener, KeyListener{
	
  public static FlappyBird flappyBird;
	
  public final int WIDTH = 700, HEIGHT = 700;
  
  public Renderer renderer;
	
	//Creates multiple columns
	
  public ArrayList<Rectangle> columns;
	
	//Creates random height spaces between the columns 
 	
  public Random rand;
	
	 //Draws the bird in the shape of a rectangle
   
   public Rectangle bird;
	
	//Initializes that the game is over when the bird has hit something
	//The bird wont move until the game has stated
  
  public boolean gameOver, started;
	
	//Creates the motion of the bird
	//Movement of the bird
	
  public int ticks, yMotion, score;
  private int getKeyCode;
	
  public FlappyBird(){
		//Sets up the jframe window
		
    JFrame jframe = new JFrame();
		
		//Repaints the program
    
    Timer timer = new Timer(20, this);
		
		renderer = new Renderer();
		rand = new Random();
		
		jframe.add(renderer);
		jframe.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		jframe.setSize(WIDTH, HEIGHT);
		jframe.addMouseListener(this);
		jframe.addKeyListener(this);
		jframe.setTitle("Blockhead");
		jframe.setResizable(true);
		jframe.setVisible(true);
		
		//Coordinates of the rectangle
		//Draws and initializes the Rectangle 
		
    bird = new Rectangle(WIDTH / 2 - 10, HEIGHT / 2 - 10, 20, 20);
		
		//Draws and initializes the columns
		
    columns = new ArrayList<Rectangle>();
		
		addColumn(true);
		addColumn(true);
		addColumn(true);
		addColumn(true);
		
		timer.start();
	}
	
	//Adds the columns
	//Initializes the ArrayList of Columns
	
  public void addColumn(boolean start){
		//Creates space between the columns 
		int space = 300; 
		int width = 100;
		
		//Creates random heights of the columns as the game goes on
		//The minimum height is 50, while the max height it can go is 300
    
    int height = 50 + rand.nextInt(300);
		
		//If it is a starting column
		
    if(start){
			//Add the top columns
		
    columns.add(new Rectangle(WIDTH + width + columns.size() * 300, 
				HEIGHT - height - 120, width, height));
			
      //Adds the bottom columns
			
      columns.add(new Rectangle(WIDTH + width + (columns.size() - 1) * 300,
				0, width, HEIGHT - height - space));
		}
		//Takes the last column from the ArrayList
		//Basically adds another column after the first column
		
    else{
			//Add the top columns
			columns.add(new Rectangle(columns.get(columns.size()-1).x + 600, 
				HEIGHT - height - 120, width, height));
			//Adds the bottom columns
		
    columns.add(new Rectangle(columns.get(columns.size()-1).x,
				0, width, HEIGHT- height - space));
		}
		
	}
	
	//Click on the screen to start the game

public void jump(){
		if(gameOver){
			
			//Coordinates of the rectangle
			//Draws and initializes the Rectangle 
		
    bird = new Rectangle(WIDTH / 2 - 10, HEIGHT / 2 - 10, 20, 20);
			
			//Cleares the columns
			
      columns.clear();
			
			yMotion = 0;
			
			score = 0;
	
			addColumn(true);
			addColumn(true);
			addColumn(true);
			addColumn(true);
			
			gameOver = false;
		}
		
		if(!started){
			started = true;
		}
		else if(!gameOver){
			if(yMotion > 0){
				yMotion = 0;
			}
			
			yMotion -= 10;
		}
		
	}
	
	//Draws the column pipes whenever you need them
	//The Array List creates multiple columns
	
  public void paintColumn(Graphics g, Rectangle column){
		//Draws the columns 
		g.setColor(Color.yellow.darker());
		//If there are any other columns it will move over to the next one
		g.fillRect(column.x, column.y, column.width, column.height);	
	}
	
	@Override
	public void actionPerformed(ActionEvent arg0) {
		//The speed of the bird
	
  int speed = 2;
		
		//Allows the bird to move
		
    ticks++;
		
		if(started){
		
		//The speed of the columns moving along the game
		
    for(int i = 0; i < columns.size(); i++){
			Rectangle column = columns.get(i);
			column.x -= 10;
		}
		
		//The motion of the bird
		
    if(ticks % 2 == 0 && yMotion < 7){
			yMotion += 2;
		}
		
		//The speed of the columns moving along the game
		//Creates an infinite loop of columns
		//Adds columns to the screen one at a time
		
    for(int i = 0; i < columns.size(); i++){
			Rectangle column = columns.get(i);
			
			if(column.x + column.width < 0){
				
				columns.remove(column); //Removes the column once the bird has pass through it
				if(column.y == 0){ //Adds the other column
					addColumn(false);
				}	
			}
		}
		
		//Movement of the bird
		//The bird moves up and down along the y-axis
		
    bird.y -= yMotion;
		
		
		for(Rectangle column : columns){
			//Adds the score to the game each time you successfully move though the two columns
			
      if(column.y == 0 && bird.x + bird.width/2 > column.x + column.width/2 - 5 &&
					bird.x + bird.width/2 < column.x + column.width/2 + 5){
				score++;
			}
			
			//Collision detection
			//Test the collision between the bird and the columns
			//If the bird hits one of the tiles then the game is over
      
      if(column.intersects(bird)){
				gameOver = true;
				
				//The column moves the bird when its on the floor
				
        if(bird.x <= column.x - bird.width){
					bird.x = column.x - bird.width;
				}
				else{
					if(column.y != 0){
						bird.y = column.y - bird.height;
					}
					else if(bird.y < column.height){
						bird.y = column.height;
					}
				}
			}
		}
		
		//Collision detection
		//If the bird touches the ground or the ceiling then the game over
		
    if(bird.y > HEIGHT - 120 || bird.y < 0){
			
			gameOver = true;
		}
		
		if(bird.y + yMotion >= HEIGHT){
			bird.y = HEIGHT - 120 - bird.height;
		}
	 }
		renderer.repaint();
  }

	//Repaints the program onto the JPanel
	//Adds the color to the objects such as the bird and the pipes
	
  public void repaint(Graphics g) {
		//Draws the background
		g.setColor(Color.black);
		g.fillRect(0, 0, WIDTH, HEIGHT);
		
		//Draws the bird
    
    g.setColor(Color.green);
		g.fillRect(bird.x, bird.y, bird.width, bird.height);
		
		//Draws the ground
		
    g.setColor(Color.MAGENTA);
		g.fillRect(0, HEIGHT-120, WIDTH, 150);
		
		//Draws another part of the ground that represents grass
		
    g.setColor(Color.blue);
		g.fillRect(0, HEIGHT-120, WIDTH, 20);
		
		//Draws the columns
		
    for(Rectangle column : columns){
			paintColumn(g, column);
		}
		
		//Displays the "Click to start" text
		//Displays the text "Game Over" if the game is over
		
    g.setColor(Color.RED);
		g.setFont(new Font("Arial", 1, 100));
		
		//Draws the click to start text to the screen before the game starts
		if(!started){
			g.drawString("Click to start!", 100, HEIGHT/2 - 50);
		}
		
		//Draws the game over text to the screen when the bird hits something
		
    if(gameOver){
			g.drawString("Game Over!", 100, HEIGHT/2 - 50);
		}
		
		//Draws the score to the screen
		
    if(!gameOver && started){
			g.drawString(String.valueOf(score), WIDTH/2 - 25, 100);
		}
	}
	
	public static void main(String[] args){
		flappyBird = new FlappyBird();
	}

	@Override
	public void mouseClicked(MouseEvent arg0) {
		jump();
	}
	
	@Override
	public void mouseEntered(MouseEvent arg0) {
		
	}

	@Override
	public void mouseExited(MouseEvent arg0) {
		
	}

	@Override
	public void mousePressed(MouseEvent arg0) {
		
	}

	@Override
	public void mouseReleased(MouseEvent arg0) {
		
	}

	@Override
	public void keyPressed(KeyEvent e) {
		if(e.getKeyCode() == KeyEvent.VK_SPACE){
			jump();
		}
	}

	@Override
	public void keyReleased(KeyEvent e) {
		if(e.getKeyCode() == KeyEvent.VK_SPACE){
			jump();
		}
	}

	@Override
	public void keyTyped(KeyEvent e) {
		
	}
}
