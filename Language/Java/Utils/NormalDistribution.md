# 正态分布
````
import java.text.DecimalFormat;
import java.util.*;

public class NormalDistribution {

    public static void main(String[] args) {
//        double a = gaussian(0.0,1.5,0.0,1);
//        double b = gaussian(0.0,3.0,0.0,1);
//        double c = gaussian(0.0,4.0,0.0,1);
        DecimalFormat df = new DecimalFormat("0");
        DecimalFormat df2 = new DecimalFormat("0.0");
        Map<Double,Double> test = genUserNum();
        for(double i =0;i<24;i=i+1) {
            double value = (double)test.get(i);
            System.out.println(String.valueOf(df2.format(i))+":      "+String.valueOf(df.format(value)));
        }
//        for (double key:test.keySet()) {
//            double value = (double)test.get(key);
//            System.out.println(String.valueOf(df2.format(key))+":      "+String.valueOf(df.format(value)));
//        }
    }

    public static Map genUserNum()
    {
        Map<Double,Double> result = new HashMap<>();
        List<Map<String,Double>> peakTimes = new ArrayList<>();
        double random_o =  Math.random()/100000;
        // random_o = 0;
        // 通行距离大于30
        peakTimes.add(addPeakTime(0,2,1+random_o,0,3478984/100));
        peakTimes.add(addPeakTime(2,9,1.5+random_o,7,3478984));
        peakTimes.add(addPeakTime(9,16,2.5+random_o,13,3478984*2));
        peakTimes.add(addPeakTime(16,22,1.503+random_o,19,3478984));
        peakTimes.add(addPeakTime(22,24,1+random_o,24,3478984/100));
        for (Map peakTime:peakTimes) {
            double start = (double)peakTime.get("beginTime");
            double end = (double)peakTime.get("endTime");
            double o = (double)peakTime.get("o");
            double u = (double)peakTime.get("u");
            double scale = (double)peakTime.get("scale");
            for(double i = start;i<end;i=i+1)
            {
                result.put(i,gaussian(i,o,u,scale));
            }
        }
        return result;
    }
    // 某个峰值开始时间,结束时间,以及对应的 o u 值
    // 正态分布模拟后,对值放大的倍数,建议4000左右
    public static Map addPeakTime(double beginTime,double endTime, double o, double u,double scale)
    {
        Map<String,Double> time9 = new HashMap();
        time9.put("beginTime",beginTime);
        time9.put("endTime",endTime);
        time9.put("o",o);
        time9.put("u",u);
        time9.put("scale",scale);
        return time9;
    }
    // x代表坐标,自变量.
    // o 代表尺度参数,决定分布的幅度.值越大越平缓(必须>0,建议小于3,否则太过平缓)
    // u 代表位置参数,决定分布的位置.值与最高点的坐标有关
    // scale 约等于 峰值人数/峰值比率, 即 scale*峰值概率 = 峰值人数
    public static double gaussian(double x,double o,double u,double scale)
    {
        double coeff1 = (float)1/(Math.sqrt(2.0 * (float)Math.PI)*o);
        double coeff2 = -1/(2*Math.pow(o,2));
        double coeff3 =  Math.pow(Math.E, coeff2* Math.pow(x-u, 2));
        return coeff1*coeff3*scale;
    }
}

````
