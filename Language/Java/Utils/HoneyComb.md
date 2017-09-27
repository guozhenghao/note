# 蜂窝网格生成算法

传入区域对角线两点，生成蜂窝网格，可以得到网格中心点或边界点
````
import java.util.ArrayList;
import java.util.List;

public class BeeHiveCell {

	public static List getBeeCell(double[] lngy,double[] latx,int[] cell) {
		double x = (latx[1]-latx[0])/cell[0];
		double y = (lngy[1]-lngy[0])/cell[1];

		double g_unitx = x*1.7320508;
		double g_unity = y*1.5;

		int jmax = (int) ((lngy[1]- lngy[0])/g_unity+1);
		int imax = (int) ((latx[1]- latx[0])/g_unitx+1);

		//绘制蜂窝网格
		StringBuffer sb = new StringBuffer();
		double cx = 0;
		double cy = 0;
		List list = new ArrayList<>();
		for(int j=0; j < jmax; j++){
			for(int i=0; i< imax; i++)
			{
				//如果是奇数，向右移动半个格子
				if(j%2 == 0)
					cx = (double)(latx[0]+g_unitx*i);
				else
					cx = (double)(latx[0]+g_unitx*(i+0.5));
				cy = (double)(lngy[0]+g_unity * j);

				List<double[]> ds = GetCellPoints(cx, cy, y,g_unitx);
				list.add(ds);
			}
		}
		return list;
	}
	static List<double[]> GetCellPoints(double cx, double cy, double a,double g_unitx){
		List<double[]> ds = new ArrayList<>();
		double[] point0 = new double[2];
		double[] point1 = new double[2];
		double[] point2 = new double[2];
		double[] point3 = new double[2];
		double[] point4 = new double[2];
		double[] point5 = new double[2];

		point0[0] = cx;
		point0[1] = cy - a;

		point1[0] = cx + (double)(g_unitx*0.5);
		point1[1] = cy - a/2; 

		point2[0] = point1[0];
		point2[1] = cy + a/2;

		point3[0] = cx;
		point3[1] = cy + a;

		point4[0] = cx - (double)(g_unitx*0.5);
		point4[1] = cy + a/2;

		point5[0] = point4[0];
		point5[1] = cy - a/2;

		ds.add(point0);
		ds.add(point1);
		ds.add(point2);
		ds.add(point3);
		ds.add(point4);
		ds.add(point5);
		return ds;
	}

	public static void main(String[] args) {
		double[] lngy= {39.736763, 40.090680};
		double[] latx = {116.180420, 116.641846};
		int cell[] = {30,30};
		System.out.println(BeeHiveCell.getBeeCell(lngy, latx, cell));
	}
}
````