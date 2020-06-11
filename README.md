# CMF_IndicatorMQL5

Chaikin Money Flow Indicator for MQL5 - Source
---
Copy and Use as you wish...
---
it is not 100% done, but works well as is.

```
//https://github.com/IgorFaggiano/CMF_IndicatorMQL5

#property description "Chaikin Money Flow"

#property indicator_separate_window
#property indicator_buffers 4
#property indicator_plots   1
#property indicator_level1  0

#property indicator_levelstyle 0
#property indicator_levelwidth 2
#property indicator_levelcolor clrDarkCyan

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
   if(rates_total<UsedPeriods)
      return(0);
   
   int initialPosition = prev_calculated -1;
   if(initialPosition<0) initialPosition = 0;
   
   if(InpVolumeType==VOLUME_TICK)
   {
      for(int pos = initialPosition;pos<=rates_total-UsedPeriods;pos++)
      {
         double sumAdTotal = 0;
         long sumVolume = 0;
         
         for(int iPeriod = 0; iPeriod < UsedPeriods && !IsStopped(); ++iPeriod)
         {
            long thisTickVolume = tick_volume[pos+iPeriod];
            sumVolume += thisTickVolume;
            sumAdTotal += AD(high[pos+iPeriod], low[pos+iPeriod], close[pos+iPeriod], thisTickVolume);
         }
         
         ExtCMFBuffer[pos+UsedPeriods-1] = sumAdTotal/sumVolume;
      }
   }
   else
   {
      for(int pos = initialPosition;pos<=rates_total-UsedPeriods;pos++)
      {
         double sumAdTotal = 0;
         long sumVolume = 0;
         
         for(int iPeriod = 0; iPeriod < UsedPeriods && !IsStopped(); ++iPeriod)
         {
            long thisTickVolume = volume[pos+iPeriod];
            sumVolume += thisTickVolume;
            sumAdTotal += AD(high[pos+iPeriod], low[pos+iPeriod], close[pos+iPeriod], thisTickVolume);
         }
         
         ExtCMFBuffer[pos+UsedPeriods-1] = sumAdTotal/sumVolume;
      }
   }
   
   return (rates_total-UsedPeriods-10);
}
```