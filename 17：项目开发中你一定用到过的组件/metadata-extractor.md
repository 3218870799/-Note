简介

metadata-extractor允许您通过简单的API访问数字图像和视频中的元数据。

支持的图像文件类型：

JPEG PNG WebP GIF ICO BMP TIFF PSD PCX RAW CRW CR2 NEF ORF RAF RW2 RWL SRW ARW DNG X3F

支持的视频文件类型：

MOV MP4 M4V 3G2 3GP 3GP

元数据格式：

Exif IPTC XMP JFIF JFXX ICC 8BIM









引入依赖

```xml
<dependency>
    <groupId>com.drewnoakes</groupId>
    <artifactId>metadata-extractor</artifactId>
    <version></version>
</dependency>
```

使用

```java
import com.drew.metadata.*;
import com.drew.metadata.exif.*;
import com.drew.imaging.jpeg.*;
import com.drew.lang.*;
import java.io.*;

public class ImageGeo {
	public double lat = 0.0;
	public double lon = 0.0;
	public double alt = 0.0;
	public boolean error = false;

	public ImageGeo(String filename) {
		try {
			error = false;
			File jpegFile = new File(filename);
			Metadata metadata = JpegMetadataReader.readMetadata(jpegFile);

			GpsDirectory gpsdir = (GpsDirectory) metadata
					.getDirectory(GpsDirectory.class);
			Rational latpart[] = gpsdir
					.getRationalArray(GpsDirectory.TAG_GPS_LATITUDE);
			Rational lonpart[] = gpsdir
					.getRationalArray(GpsDirectory.TAG_GPS_LONGITUDE);
			String northing = gpsdir
					.getString(GpsDirectory.TAG_GPS_LATITUDE_REF);
			String easting = gpsdir
					.getString(GpsDirectory.TAG_GPS_LONGITUDE_REF);

			try {
				alt = gpsdir.getDouble(GpsDirectory.TAG_GPS_ALTITUDE);
			} catch (Exception ex) {
			}

			double latsign = 1.0d;
			if (northing.equalsIgnoreCase("S"))
				latsign = -1.0d;
			double lonsign = 1.0d;
			if (easting.equalsIgnoreCase("W"))
				lonsign = -1.0d;
			lat = (Math.abs(latpart[0].doubleValue())
					+ latpart[1].doubleValue() / 60.0d + latpart[2]
					.doubleValue() / 3600.0d) * latsign;
			lon = (Math.abs(lonpart[0].doubleValue())
					+ lonpart[1].doubleValue() / 60.0d + lonpart[2]
					.doubleValue() / 3600.0d) * lonsign;

			if (Double.isNaN(lat) || Double.isNaN(lon))
				error = true;
		} catch (Exception ex) {
			error = true;
		}
		System.out.println(filename + ": (" + lat + ", " + lon + ")");
	}
	
	public static void main(String[] args) {
		ImageGeo imageGeo = new ImageGeo(ImageGeo.class.getResource("IMAG0068.jpg").getFile());
		System.out.println(imageGeo.lon+","+imageGeo.lat);
	}

}
```

