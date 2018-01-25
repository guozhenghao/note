# 绘制抛物线
### 老版抛物线绘制
传入两点坐标，不支持分别设置两点高度，只能为地面上两点
- 使用两点式进行计算，默认45度入射，如果需要修改或者设置为传参方式，修改centerHigh参数即可
````

import java.util.LinkedList;
import java.util.List;
/**
* 计算抛物线
* @author gzh
* @Time 2017-08-31
*/
public class Parabola {
	
	private double lon1;//lonlat为两个点的坐标
	private double lat1;
	private double lon2;
	private double lat2;
	private int count;//需要的点数
	
	public Parabola(double lon1, double lat1, double lon2, double lat2,int count) {
		this.lon1 = lon1;
		this.lat1 = lat1;
		this.lon2 = lon2;
		this.lat2 = lat2;
		this.count = count;
	}
	
	public List get(){
		//使用两点式，给出的两个点的当做与x轴的焦点
		//将一个点当做原点，然后带入中心点坐标即可求出a
		//y = a(x-X1)(x-X2)
		List result = new LinkedList<>();
		result = getAllPoint();
		return result;
	}
	/**
	 * 计算某点的高度
	 * @param x 该点在坐标系中的横坐标
	 * @param a 方程中的参数
	 * @param lengthX 坐标轴两焦点间距离
	 * @return result 高度
	 */
	public double getH(double x,double a,double lengthX){
		
		double result = a*(Math.pow(x, 2) - x*lengthX);
        //高度转换为米
		result = result * 1000 * 111;
		return result;
	}
	/**
	 * 计算所有点的坐标
	 * @return result 抛物线中所有点的坐标
	 */
	public List getAllPoint(){
		// 转换到坐标系中两点间距离
		double lengthX = Math.sqrt(Math.pow((lon1 - lon2), 2) + Math.pow((lat1 - lat2),2));
		// 中点的高度，按照入射角45°计算
		double centerHigh = lengthX / 2 ;
		// sincos用于将坐标系中的点坐标，转换为经纬度
		double sinIndex = (lat2 - lat1) / lengthX;
		double cosIndex = (lon2 - lon1) / lengthX;
		// a为两点式中的参数
		double a = centerHigh / (Math.pow(lengthX/2, 2) - lengthX/2 * lengthX);
		// length为坐标系中相邻两点的距离
		double length = lengthX / count;
		// 临时变量，抛物线中某点的坐标
		double high;
		double lon;
		double lat;
		List result = new LinkedList<>();
		for(int i = 0;i <= count;i++){
			high = getH(i * length,a,lengthX);
			lon = lon1 + length * cosIndex * i;
			lat = lat1 + length * sinIndex * i;
			List temp = new LinkedList();
			temp.add(lon);
			temp.add(lat);
			temp.add(high);
			result.add(temp);
		}
		return result;
	}
	
	public static void main(String[] args){
		
		Parabola pa = new Parabola(78.8708, 41.402, 73.4875, 37.6501,100);
		List list = pa.get();
		System.out.println(list.toString());
		
	}
	
}

````

### 新版抛物线
传入两点坐标包括高度(单位:m),如果为平面上的点，高度置0即可
- 使用克拉默法则进行计算，默认45度入射，如果需要修改或者设置为传参方式，修改centerHigh参数即可
- 若两点高度不一样，则中点不在中间，入射会变大或变小，主要是看这个定义的中点离高点近还是远点近
````

import java.util.LinkedList;
import java.util.List;
/**
* 计算抛物线
* @author gzh
* @Time 2018-01-24
*/
public class ParabolaNew {
	
	//lonlathigh为点的坐标和高度
	private double lon1;
	private double lat1;
	private double high1;
	private double lon2;
	private double lat2;
	private double high2;
	//需要的点数
	private int count;
	
	public ParabolaNew(double lon1, double lat1, double high1,  double lon2, double lat2,double high2,int count) {
		this.lon1 = lon1;
		this.lat1 = lat1;
		//将高度转换为度 计算时单位统一
		this.high1 = high1 / 1000 / 111;
		this.lon2 = lon2;
		this.lat2 = lat2;
		this.high2 = high2 / 1000 / 111;
		this.count = count;
	}
	
	public List get(){
		//使用两点式，给出的两个点的当做与x轴的焦点
		//将一个点当做原点，然后带入中心点坐标即可求出a
		//y = a(x-X1)(x-X2)
		List result = new LinkedList<>();
		result = getAllPoint();
		return result;
	}
	/**
	 * 计算某点的高度
	 * @param x 该点在坐标系中的横坐标
	 * @param a 方程中的参数
	 * @param lengthX 坐标轴两焦点间距离
	 * @return result 高度
	 */
	public double getH(double x,double[] parameterResult,double lengthX){
		
		double result = parameterResult[0] * Math.pow(x, 2) + parameterResult[1] * x + parameterResult[2];
		//高度需要的是米，将度转换成米
		result = result * 1000 * 100;
		return result;
	}
	/**
	 * 计算所有点的坐标
	 * @return result 抛物线中所有点的坐标
	 */
	public List getAllPoint(){
		// 转换到坐标系中两点间距离
		double lengthX = Math.sqrt(Math.pow((lon1 - lon2), 2) + Math.pow((lat1 - lat2),2));
		// 中点的高度，按照入射角45°计算
		double centerHigh = lengthX / 2 + (high1 > high2 ? high1 : high2); 
		// sincos用于将坐标系中的点坐标，转换为经纬度
		double sinIndex = (lat2 - lat1) / lengthX;
		double cosIndex = (lon2 - lon1) / lengthX;
		// 带入三点坐标后等式参数
		double[][] parameter = new double[3][3];
		//带入第一个点后，等式y的另一侧a/b/c前的参数
		parameter[0][0] = 0;
		parameter[0][1] = 0;
		parameter[0][2] = 1;
		//带入中心点后，等式y的另一侧a/b/c前的参数
		parameter[1][0] = Math.pow((lon1 - lon2)/2, 2) + Math.pow((lat1 - lat2)/2,2);
		parameter[1][1] = Math.sqrt(Math.pow((lon1 - lon2)/2, 2) + Math.pow((lat1 - lat2)/2,2));
		parameter[1][2] = 1;
		//带入第二个点后，等式y的另一侧a/b/c前的参数
		parameter[2][0] = Math.pow(lengthX, 2);
		parameter[2][1] = lengthX;
		parameter[2][2] = 1;
		// 带入三点坐标后等式值
		double[] parameterValue = {high1,centerHigh,high2};
		// 通过克拉默法则计算参数值
		double[] parameterResult = CramerRule(parameter,parameterValue);
		// length为坐标系中相邻两点的距离
		double length = lengthX / count;
		// 临时变量，抛物线中某点的坐标
		double high;
		double lon;
		double lat;
		List result = new LinkedList<>();
		for(int i = 0;i <= count;i++){
			high = getH(i * length,parameterResult,lengthX);
			lon = lon1 + length * cosIndex * i;
			lat = lat1 + length * sinIndex * i;
			List temp = new LinkedList();
			temp.add(lon);
			temp.add(lat);
			temp.add(high);
			result.add(temp);
		}
		return result;
	}
	
	public static void main(String[] args){
		
		ParabolaNew pa = new ParabolaNew(121.48,31.26, 500, 116.404269, 39.914935,500,100);
		List list = pa.get();
		System.out.println(list.toString());
		
	}
	
	//***************************************************************
	// 方法一：
	//y = ax^2 + bx + c
	//x1,y1    x2,y2    x3,y3
	//带入
	//y1 = ax1^2 + bx1 + c ①
	//y2 = ax2^2 + bx2 + c ②
	//y3 = ax3^2 + bx3 + c ③
	//②-①去c得④ ③-①去c得⑤
	//④⑤解出a值
	//a带入 算出①和②的倍数关系，然后两式子消原得b，再带入求c得到方程
	//缺点： 来回带入消原非常繁琐麻烦   但是可以保证正确性  以后再实现
	//***************************************************************
	// 方法二：
	//系数矩阵的秩等于增广矩阵的秩，有唯一解，将增广矩阵进行初等行变换
	//通过变换后，可以进行部分的化简，之后还得通过消原得方法求解，只不过简单一点
	//***************************************************************
	// 方法三:
	//使用克拉默法则，非齐次线性方程组系数矩阵可逆，则有唯一解
	//使用此方法进行计算
	//***************************************************************
	
	//使用克拉默法则进行函数构建
	public double[] CramerRule(double[][] parameter,double[] parameterResult){
		//构建行列式并计算克拉默参数
		double d = parameter[0][0] *  parameter[1][1] * parameter[2][2] + parameter[0][1] *  parameter[1][2] * parameter[2][0] + parameter[1][0] *  parameter[2][1] * parameter[0][2] 
					- parameter[0][2] *  parameter[1][1] * parameter[2][0] - parameter[2][1] *  parameter[1][2] * parameter[0][0] - parameter[1][0] *  parameter[0][1] * parameter[2][2];
		double da = parameterResult[0] *  parameter[1][1] * parameter[2][2] + parameter[0][1] *  parameter[1][2] * parameterResult[2] + parameterResult[1] *  parameter[2][1] * parameter[0][2] 
				- parameter[0][2] *  parameter[1][1] * parameterResult[2] - parameter[2][1] *  parameter[1][2] * parameterResult[0] - parameterResult[1] *  parameter[0][1] * parameter[2][2];
		double db = parameter[0][0] *  parameterResult[1] * parameter[2][2] + parameterResult[0] *  parameter[1][2] * parameter[2][0] + parameter[1][0] *  parameterResult[2] * parameter[0][2] 
				- parameter[0][2] *  parameterResult[1] * parameter[2][0] - parameterResult[2] *  parameter[1][2] * parameter[0][0] - parameter[1][0] *  parameterResult[0] * parameter[2][2];
		double dc = parameter[0][0] *  parameter[1][1] * parameterResult[2] + parameter[0][1] *  parameterResult[1] * parameter[2][0] + parameter[1][0] *  parameter[2][1] * parameterResult[0] 
				- parameterResult[0] *  parameter[1][1] * parameter[2][0] - parameter[2][1] *  parameterResult[1] * parameter[0][0] - parameter[1][0] *  parameter[0][1] * parameterResult[2];
		//计算出参数a、b、c的值
		double a = da/d;
		double b = db/d;
		double c = dc/d;
		double[] result = {a,b,c};
		return result;
	}	
}

````