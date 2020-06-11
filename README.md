# CMF_IndicatorMQL5
Chaikin Money Flow Indicator for MQL5 - Source
Copy and Use as you wish...
it is not 100% done, but works well as is.

```
#property description "Chaikin Money Flow"

//--- indicator settings
#property indicator_separate_window
#property indicator_buffers 4
#property indicator_plots   1
#property indicator_type1   DRAW_LINE
#property indicator_color1  LightSeaGreen

input int                 UsedPeriods=20; // Used Periods
input ENUM_APPLIED_VOLUME InpVolumeType=VOLUME_TICK;  // Volumes
double                    ExtCMFBuffer[];

void OnInit()
{
   SetIndexBuffer(0,ExtCMFBuffer,INDICATOR_DATA);
   IndicatorSetInteger(INDICATOR_DIGITS,5);
   PlotIndexSetInteger(0,PLOT_DRAW_BEGIN,0);
   IndicatorSetString(INDICATOR_SHORTNAME,"Chaikin Money Flow("+string(UsedPeriods)+")");
}

double AD(double high,double low,double close,long volume)
{
   double res=0;
   
   if(high!=low)
      res=(2*close-high-low)/(high-low)*volume;
   
   return(res);
}

int OnCalculate(const int rates_total,
                const int prev_calculated,
                const datetime &time[],
                const double &open[],
                const double &high[],
                const double &low[],
                const double &close[],
                const long &tick_volume[],
                const long &volume[],
                const int &spread[])
{
   int i, i2, limit;
   double sumAdTotal;
   long sumVolume;
   
   if(rates_total<UsedPeriods)
      return(0);

   limit = prev_calculated -1; //-1 pq é index
   if(limit<0) limit=0;
      
   if(InpVolumeType==VOLUME_TICK)
   {
      for(i=limit;i<rates_total && !IsStopped();i++)
      {
         sumAdTotal = 0;
         sumVolume = 0;
         
         for(i2 = 0; i2 < UsedPeriods && !IsStopped(); ++i2)
         {
            long thisTickVolume = tick_volume[i+i2];
            sumVolume += thisTickVolume;
            sumAdTotal += AD(high[i+i2], low[i+i2], close[i+i2], thisTickVolume);
         }
         
         ExtCMFBuffer[i+UsedPeriods-1] = sumAdTotal/sumVolume;
      }
   }
   else
   {
      for(i=limit;i<rates_total && !IsStopped();i++)
      {
         sumAdTotal = 0;
         sumVolume = 0;
         
         for(i2 = 0; i2 < UsedPeriods && !IsStopped(); ++i2)
         {
            long thisVolume = volume[i+i2];
            sumVolume += thisVolume;
            sumAdTotal += AD(high[i+i2], low[i+i2], close[i+i2], thisVolume);
         }
         
         ExtCMFBuffer[i+UsedPeriods-1] = sumAdTotal/sumVolume;
      }
   }
   
   return(rates_total);
}
```