# Asteroid_Dodge
import java.awt.Color;
	import java.awt.Graphics;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.image.BufferedImage;
	import java.io.File;
import java.util.ArrayList;
import java.util.Random;

	import javax.imageio.ImageIO;
	import javax.swing.JFrame;
	import javax.swing.JPanel;

	public class AsteroidDodge<ThrustImage> extends JFrame
	{
		private int height = 760, width = 977, hexSize = 110; //  760 x 977
		private int hexWidth = 90; 
	    private double deltaY = hexWidth / 2.0 * Math.sqrt(3); 
	    private int originX = 353, originY = 310;
	    private int yChange = 75;
	   
	    private ArrayList<Asteroid> asteroids;
	    private ArrayList<BufferedImage> rockets;
	    private BasicRocket rocky;
	    public Random randy = new Random();
//		private Thread thread = new Thread();

		BufferedImage hexMap, thrustImage, stopImage, rightImage, reverseImage, leftImage;
		
		public void makeEnvironment()
		{
			Asteroid dummy = new Asteroid();
			asteroids = dummy.populateAsteroidList();
			rocky = new BasicRocket();
			rockets = rocky.getRocketImages();
			
			setTitle("Asteroid Dodge");
			setBounds(0, 0, width, height);
			setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
			MyPanel pan = new MyPanel();
			pan.setBackground(Color.black);
			getContentPane().add(pan);
			setResizable(true);
			setVisible(true);
			try{hexMap = ImageIO.read(new File("HexMap.png"));}
			catch(Exception e){System.out.println(e);}
			try{thrustImage = ImageIO.read(new File("Thrust.png"));}
			catch(Exception e){System.out.println(e);}
			try{stopImage = ImageIO.read(new File("Stop.png"));}
			catch(Exception e){System.out.println(e);}
			try{rightImage = ImageIO.read(new File("Right90.png"));}
			catch(Exception e){System.out.println(e);}
			try{reverseImage = ImageIO.read(new File("Reverse180.png"));}
			catch(Exception e){System.out.println(e);}
			try{leftImage = ImageIO.read(new File("Left90.png"));}
			catch(Exception e){System.out.println(e);}
			
			addMouseListener (new Clicks());
			addKeyListener(new Keys());
			rollDice();
			printDieRoll();
			repaint();
		}
		
		private class MyPanel extends JPanel
		{
			public void paintComponent(Graphics g)
			{
				super.paintComponent(g);
				try {
					drawStuff(g);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
		
		public static void main(String[] args)
		{
			AsteroidDodge obj = new AsteroidDodge();
			obj.makeEnvironment();
		}
		public void drawStuff(Graphics g) throws InterruptedException
		{
		//	thread.sleep(1);
			g.drawImage(hexMap, 0, 0, null);
		//	g.drawImage()
			for(int i = 0; i < asteroids.size(); i++)
			{
				
				Asteroid temp = asteroids.get(i);
				g.drawImage(temp.getImage(), 
						(int) (originX + temp.getgLeftRight()*hexWidth),
						(int) (originY - temp.getUpDown()*deltaY),
						110, 110, null);
			}
			g.drawImage(rockets.get(rocky.getDirection() -1), 
					(int) (originX + rocky.getRocketX() *hexWidth), 
					(int) (originY - rocky.getRocketY() * deltaY), 
					hexSize, hexSize,  null);
			g.drawImage(thrustImage, 775, 5, 50, 70, null);
			g.drawImage(stopImage, 775, 5 + yChange, 50, 70, null);
			g.drawImage(rightImage, 775, 5 + 2 * yChange, 50,  70, null);
			g.drawImage(reverseImage, 775, 5 + 3 * yChange, 50, 70, null);
			g.drawImage(leftImage, 775, 5 + 4 * yChange, 50, 70, null);
			
			//repaint();
		}
		public void rollDice()
		{
			int roll = randy.nextInt(6)+1; //red roll
			asteroids.get(0).setDieRoll(roll); //red asteroid
			asteroids.get(1).setDieRoll(roll);//red ringed
			
			roll = randy.nextInt(6)+1; //white roll
			asteroids.get(2).setDieRoll(roll);//white asteroid
			asteroids.get(3).setDieRoll(roll);//white ringed
			
			roll = randy.nextInt(6)+1;//blue roll
			asteroids.get(4).setDieRoll(roll); //blue asteroid
			asteroids.get(5).setDieRoll(roll); //blue ringed
			}
		public void printDieRoll() //Temporary
		{
			System.out.println("Red Die:" + asteroids.get(0).getDieRoll() + "\n" + 
					"White Die:" + asteroids.get(3).getDieRoll() + "\n" +
					"Blue Die:" + asteroids.get(5).getDieRoll() + "\n");
		}
		private class Keys extends KeyAdapter
		{
			public void keyPressed (KeyEvent e)
			{
				int keyCode = e.getKeyCode();
				if (keyCode == e.VK_NUMPAD8) //Thrust
				{
					rocky.thrust();
				}
				if (keyCode == e.VK_NUMPAD6) //turnRight
				{
					rocky.turnRight();
				}
				if (keyCode == e.VK_NUMPAD4) //turnLeft
				{
					rocky.turnLeft();
				}
				if (keyCode == e.VK_NUMPAD2) //reverse
				{
					rocky.reverse();
				}
				if(keyCode == e.VK_SPACE) //if spacebar is hit simulate asteroid movemnts
				{
					for(int i = 0; i < asteroids.size(); i++)
					{
						Asteroid temp = asteroids.get(i);
						temp.move(temp.getDieRoll());
					}
					rollDice();
					printDieRoll();
				}
				repaint();
			}
		}//end of the Key's class
		
		private class Clicks extends MouseAdapter
		{
			int mouseX = 0;
			int mouseY = 0;
			int fudgeX = 8, fudgeY = 31;
			public void mousePressed (MouseEvent e)
			{
				mouseX = e.getX();
				mouseY = e.getY();
				if (mouseX >= 775 + fudgeX && mouseX <= 825 + fudgeX && mouseY >= 5 + fudgeY && mouseY <= 75)
				{
					rocky.thrust();
				}
				System.out.println("(" + mouseX + "," + ")");
			
				if (mouseX >= 775 + fudgeX && mouseX <= 825 + fudgeX && mouseY >= 155 + fudgeY && mouseY <= 225)
				{
					rocky.turnRight();
				}
				if (mouseX >= 775 + fudgeX && mouseX <= 825 + fudgeX && mouseY >= 230 + fudgeY && mouseY <= 300)
				{
					rocky.reverse();
				}
				if (mouseX >= 775 + fudgeX && mouseX <= 825 + fudgeX && mouseY >= 305 + fudgeY && mouseY <= 375)
				{
					rocky.turnLeft();
				}
				rocketCollidesWithAsteroid();
			repaint();
			
			}
			public boolean rocketCollidesWithAsteroid()
			{
				double rocketX= rocky.getRocketX();
				int rocketY = rocky.getRocketY();
				double asteroidX = -10;
				int asteroidY = -10;
				for(int i = 0; i < asteroids.size(); i++)
				{
					Asteroid temp = asteroids.get(i);
					asteroidX = temp.getgLeftRight();
					asteroidY = temp.getUpDown();
					if(rocketX == asteroidX && rocketY == asteroidY)
					{
						System.out.println("DAMAGE TAKEN!");
						return true;
					}
				}
				return false;
					}
		}
	}//end of AD class

