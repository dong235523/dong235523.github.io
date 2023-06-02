---
title: JAVA实现坐标区域碰撞
date: 2023-04-20 09:58:07
tags: 
  - 工具类
categories: 后端技术
cover: false
---
判断坐标是否在坐标串所画的区域内
```
	public static void main(String[] args){
		String zbc= "123.442907,41.820635,123.442889,41.818592,"
				+ "123.442853,41.816777,123.446428,41.816831,123.449339,41.816845,123.451459,41.816912,"
				+ "123.451333,41.819116,123.451315,41.820836,123.448836,41.820917,"
				+ "123.448979,41.818189,123.444847,41.81835,123.444757,41.820836";
		String zbx = "123.443893";
		String zby = "41.820155";
		List<XlAppGeoPoint> ps = new ArrayList<XlAppGeoPoint>();
		ps = getGeoList(zbc).get(0);
		boolean isIn = isPointInPoly(Double.parseDouble(zbx),Double.parseDouble(zby),ps);
		System.out.println(isIn);
	}
	
	private static List<List<XlAppGeoPoint>> getGeoList(String bjzbz) {
		List<List<XlAppGeoPoint>> list = new ArrayList<>();
		try {
			String[] geoArray = bjzbz.split(";");
			if (geoArray != null) {
				for (String geoString : geoArray) {
					List<XlAppGeoPoint> geoPointList = new ArrayList<>();
					String[] zbxy = geoString.split(",");
					if (zbxy != null && zbxy.length >= 2) {
						for (int i = 0; i < zbxy.length - 1; i = i + 2) {
							geoPointList.add(new XlAppGeoPoint(Double
									.parseDouble(zbxy[i]), Double
									.parseDouble(zbxy[i + 1])));
						}
					}

					list.add(geoPointList);
				}
			}
		} catch (Exception e) {

		}
		return list;
	}
	
	private static boolean isPointInPoly(double ALon, double Alat,List<XlAppGeoPoint> ps) {
		int iSum, iCount, iIndex;
		double dLon1 = 0, dLon2 = 0, dLat1 = 0, dLat2 = 0, dLon;

		iSum = 0;
		iCount = ps.size();

		for (iIndex = 0; iIndex < iCount; iIndex++) {
			if (iIndex == iCount - 1) {
				dLon1 = ps.get(iIndex).getZbx();
				dLat1 = ps.get(iIndex).getZby();
				dLon2 = ps.get(0).getZbx();
				dLat2 = ps.get(0).getZby();
			} else {
				dLon1 = ps.get(iIndex).getZbx();
				dLat1 = ps.get(iIndex).getZby();
				dLon2 = ps.get(iIndex + 1).getZbx();
				dLat2 = ps.get(iIndex + 1).getZby();
			}

			// 以下语句判断A点是否在边的两端点的水平平行线之间，在则可能有交点，开始判断交点是否在左射线上
			if (((Alat >= dLat1) && (Alat < dLat2))
					|| ((Alat >= dLat2) && (Alat < dLat1))) {
				if (Math.abs(dLat1 - dLat2) > 0) {
					// 得到A点向左射线与边的交点的x坐标
					dLon = dLon1 - ((dLon1 - dLon2) * (dLat1 - Alat))
							/ (dLat1 - dLat2);
					// 如果交点在A点左侧（说明是做射线与边的交点），则射线与边的全部交点数加一
					if (dLon < ALon) {
						iSum++;
					}
				}
			}
		}

		if ((iSum % 2) != 0) {
			return true;
		}
		return false;
	}
```

```
public class XlAppGeoPoint {
	private double zbx;
	private double zby;

	public XlAppGeoPoint(double zbx, double zby) {
		super();
		this.zbx = zbx;
		this.zby = zby;
	}
	public double getZbx() {
		return zbx;
	}
	public void setZbx(double zbx) {
		this.zbx = zbx;
	}
	public double getZby() {
		return zby;
	}
	public void setZby(double zby) {
		this.zby = zby;
	}
}
```