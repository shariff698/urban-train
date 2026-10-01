//+------------------------------------------------------------------+
//|                 PriceActionAI_EA.mq5                             |
//|       Price Action + Liquidity + BOS + FVG Expert Advisor       |
//+------------------------------------------------------------------+

#property strict
#property version   "1.00"
#property description "Price Action EA using structure, liquidity sweep, engulfing and FVG."

#include <Trade/Trade.mqh>

CTrade trade;

//==================================================================
// INPUTS
//==================================================================

//--- General
input ENUM_TIMEFRAMES AnalysisTF = PERIOD_H1;
input ENUM_TIMEFRAMES EntryTF    = PERIOD_M15;

input ulong MagicNumber = 20260927;

//--- IMPORTANT
// false = analyse only / no live orders
// true  = allow the EA to execute trades
input bool EnableTrading = false;

//--- Risk
input double RiskPercent = 1.0;
input double FixedLot    = 0.01;
input bool UseRiskSizing = true;

//--- Risk/Reward
input double RiskReward = 2.0;

//--- ATR
input int ATRPeriod = 14;
input double ATRMultiplier = 1.5;

//--- Structure
input int StructureLookback = 20;

//--- Spread protection
input double MaxSpreadPoints = 30;

//--- Price action
input bool UseLiquiditySweep = true;
input bool UseEngulfing      = true;
input bool UseFVG            = true;
input bool UseBOS            = true;

//--- Minimum confirmations
input int MinimumConfirmations = 3;

//--- Session filter
input bool UseSessionFilter = false;

// Server-time session
input int SessionStartHour = 7;
input int SessionEndHour   = 18;

//==================================================================
// GLOBAL VARIABLES
//==================================================================

datetime lastBarTime = 0;

int atrHandle = INVALID_HANDLE;

//==================================================================
// INITIALIZATION
//==================================================================

int OnInit()
{
   trade.SetExpertMagicNumber(MagicNumber);
   trade.SetDeviationInPoints(20);
   trade.SetTypeFillingBySymbol(_Symbol);

   atrHandle = iATR(_Symbol, EntryTF, ATRPeriod);

   if(atrHandle == INVALID_HANDLE)
   {
      Print("ERROR: Could not create ATR handle.");
      return(INIT_FAILED);
   }

   Print("Price Action EA initialized.");
   Print("Trading enabled: ", EnableTrading);

   return(INIT_SUCCEEDED);
}

//==================================================================
// DEINITIALIZATION
//==================================================================

void OnDeinit(const int reason)
{
   if(atrHandle != INVALID_HANDLE)
      IndicatorRelease(atrHandle);
}

//==================================================================
// ON TICK
//==================================================================

void OnTick()
{
   // Only analyze when a new EntryTF candle appears
   if(!IsNewBar())
      return;

   // Do not trade outside selected session
   if(UseSessionFilter && !InsideTradingSession())
   {
      Print("Outside trading session.");
      return;
   }

   // Spread protection
   if(!SpreadOK())
   {
      Print("Spread too high.");
      return;
   }

   // Only one position for this EA/symbol
   if(HasOpenPosition())
   {
      Print("Existing EA position found. No new trade.");
      return;
   }

   //==============================================================
   // MARKET ANALYSIS
   //==============================================================

   int trend = GetHigherTimeframeTrend();

   if(trend == 0)
   {
      Print("No clear higher-timeframe direction.");
      return;
   }

   int bullishScore = 0;
   int bearishScore = 0;

   // Liquidity sweep
   if(UseLiquiditySweep)
   {
      if(BullishLiquiditySweep())
         bullishScore++;

      if(BearishLiquiditySweep())
         bearishScore++;
   }

   // BOS
   if(UseBOS)
   {
      if(BullishBOS())
         bullishScore++;

      if(BearishBOS())
         bearishScore++;
   }

   // Engulfing
   if(UseEngulfing)
   {
      if(BullishEngulfing())
         bullishScore++;

      if(BearishEngulfing())
         bearishScore++;
   }

   // FVG
   if(UseFVG)
   {
      if(BullishFVG())
         bullishScore++;

      if(BearishFVG())
         bearishScore++;
   }

   Print(
      "Analysis: Trend=", trend,
      " BullishScore=", bullishScore,
      " BearishScore=", bearishScore
   );

   //==============================================================
   // BUY
   //==============================================================

   if(trend == 1 &&
      bullishScore >= MinimumConfirmations &&
      bullishScore > bearishScore)
   {
      Print("BUY setup detected.");

      if(EnableTrading)
         OpenBuy();
      else
         Print("BUY detected, but trading is disabled.");
   }

   //==============================================================
   // SELL
   //==============================================================

   if(trend == -1 &&
      bearishScore >= MinimumConfirmations &&
      bearishScore > bullishScore)
   {
      Print("SELL setup detected.");

      if(EnableTrading)
         OpenSell();
      else
         Print("SELL detected, but trading is disabled.");
   }
}

//==================================================================
// NEW BAR DETECTION
//==================================================================

bool IsNewBar()
{
   datetime currentBar = iTime(_Symbol, EntryTF, 0);

   if(currentBar != lastBarTime)
   {
      lastBarTime = currentBar;
      return true;
   }

   return false;
}

//==================================================================
// SESSION FILTER
//==================================================================

bool InsideTradingSession()
{
   MqlDateTime tm;
   TimeToStruct(TimeCurrent(), tm);

   int hour = tm.hour;

   if(SessionStartHour <= SessionEndHour)
   {
      return(hour >= SessionStartHour &&
             hour < SessionEndHour);
   }

   // Handles sessions crossing midnight
   return(hour >= SessionStartHour ||
          hour < SessionEndHour);
}

//==================================================================
// SPREAD
//==================================================================

bool SpreadOK()
{
   double ask = SymbolInfoDouble(_Symbol, SYMBOL_ASK);
   double bid = SymbolInfoDouble(_Symbol, SYMBOL_BID);

   double spread = (ask - bid) / _Point;

   return(spread <= MaxSpreadPoints);
}

//==================================================================
// OPEN POSITION CHECK
//==================================================================

bool HasOpenPosition()
{
   for(int i = PositionsTotal() - 1; i >= 0; i--)
   {
      ulong ticket = PositionGetTicket(i);

      if(ticket == 0)
         continue;

      if(PositionSelectByTicket(ticket))
      {
         string symbol = PositionGetString(POSITION_SYMBOL);
         long magic    = PositionGetInteger(POSITION_MAGIC);

         if(symbol == _Symbol &&
            magic == (long)MagicNumber)
         {
            return true;
         }
      }
   }

   return false;
}

//==================================================================
// HIGHER TIMEFRAME TREND
//==================================================================

int GetHigherTimeframeTrend()
{
   MqlRates rates[];

   ArraySetAsSeries(rates, true);

   int copied = CopyRates(
      _Symbol,
      AnalysisTF,
      0,
      StructureLookback + 5,
      rates
   );

   if(copied < StructureLookback)
      return 0;

   // Compare recent closed candle with older structure

   double recentHigh = rates[1].high;
   double recentLow  = rates[1].low;

   double oldHigh = rates[StructureLookback - 1].high;
   double oldLow  = rates[StructureLookback - 1].low;

   if(recentHigh > oldHigh &&
      recentLow > oldLow)
   {
      return 1; // bullish
   }

   if(recentHigh < oldHigh &&
      recentLow < oldLow)
   {
      return -1; // bearish
   }

   return 0;
}

//==================================================================
// LIQUIDITY SWEEP - BULLISH
//==================================================================

bool BullishLiquiditySweep()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 10, r) < 6)
      return false;

   // Candle 2 = sweep candle
   // Candle 1 = confirmation candle

   double previousLow = r[3].low;

   // Sweep below previous low
   bool swept =
      r[2].low < previousLow;

   // Close back above the previous low
   bool rejection =
      r[2].close > previousLow;

   // Confirmation candle bullish
   bool confirmation =
      r[1].close > r[1].open;

   return(swept && rejection && confirmation);
}

//==================================================================
// LIQUIDITY SWEEP - BEARISH
//==================================================================

bool BearishLiquiditySweep()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 10, r) < 6)
      return false;

   double previousHigh = r[3].high;

   bool swept =
      r[2].high > previousHigh;

   bool rejection =
      r[2].close < previousHigh;

   bool confirmation =
      r[1].close < r[1].open;

   return(swept && rejection && confirmation);
}

//==================================================================
// BULLISH BOS
//==================================================================

bool BullishBOS()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 10, r) < 6)
      return false;

   double previousSwingHigh = r[3].high;

   bool breakUp =
      r[1].close > previousSwingHigh;

   return breakUp;
}

//==================================================================
// BEARISH BOS
//==================================================================

bool BearishBOS()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 10, r) < 6)
      return false;

   double previousSwingLow = r[3].low;

   bool breakDown =
      r[1].close < previousSwingLow;

   return breakDown;
}

//==================================================================
// BULLISH ENGULFING
//==================================================================

bool BullishEngulfing()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 5, r) < 4)
      return false;

   bool previousBearish =
      r[2].close < r[2].open;

   bool currentBullish =
      r[1].close > r[1].open;

   bool engulf =
      r[1].open <= r[2].close &&
      r[1].close >= r[2].open;

   return(
      previousBearish &&
      currentBullish &&
      engulf
   );
}

//==================================================================
// BEARISH ENGULFING
//==================================================================

bool BearishEngulfing()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 5, r) < 4)
      return false;

   bool previousBullish =
      r[2].close > r[2].open;

   bool currentBearish =
      r[1].close < r[1].open;

   bool engulf =
      r[1].open >= r[2].close &&
      r[1].close <= r[2].open;

   return(
      previousBullish &&
      currentBearish &&
      engulf
   );
}

//==================================================================
// BULLISH FVG
//==================================================================

bool BullishFVG()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 6, r) < 5)
      return false;

   // Three-candle bullish imbalance:
   // candle 1 high < candle 3 low

   double firstHigh = r[3].high;
   double thirdLow  = r[1].low;

   return(thirdLow > firstHigh);
}

//==================================================================
// BEARISH FVG
//==================================================================

bool BearishFVG()
{
   MqlRates r[];

   ArraySetAsSeries(r, true);

   if(CopyRates(_Symbol, EntryTF, 0, 6, r) < 5)
      return false;

   // Three-candle bearish imbalance:
   // candle 1 low > candle 3 high

   double firstLow  = r[3].low;
   double thirdHigh = r[1].high;

   return(thirdHigh < firstLow);
}

//==================================================================
// ATR VALUE
//==================================================================

double GetATR()
{
   double buffer[];

   ArraySetAsSeries(buffer, true);

   if(CopyBuffer(
      atrHandle,
      0,
      1,
      1,
      buffer
   ) != 1)
   {
      return 0;
   }

   return buffer[0];
}

//==================================================================
// POSITION SIZE
//==================================================================

double CalculateLotSize(double stopDistance)
{
   if(!UseRiskSizing)
      return NormalizeLot(FixedLot);

   double balance =
      AccountInfoDouble(ACCOUNT_BALANCE);

   double riskMoney =
      balance * RiskPercent / 100.0;

   double tickSize =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_TRADE_TICK_SIZE
      );

   double tickValue =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_TRADE_TICK_VALUE
      );

   if(tickSize <= 0 ||
      tickValue <= 0 ||
      stopDistance <= 0)
   {
      return NormalizeLot(FixedLot);
   }

   double moneyPerLot =
      (stopDistance / tickSize) * tickValue;

   if(moneyPerLot <= 0)
      return NormalizeLot(FixedLot);

   double lots =
      riskMoney / moneyPerLot;

   return NormalizeLot(lots);
}

//==================================================================
// NORMALIZE LOT
//==================================================================

double NormalizeLot(double lots)
{
   double minLot =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_VOLUME_MIN
      );

   double maxLot =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_VOLUME_MAX
      );

   double step =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_VOLUME_STEP
      );

   lots =
      MathMax(minLot, lots);

   lots =
      MathMin(maxLot, lots);

   lots =
      MathFloor(lots / step) * step;

   return NormalizeDouble(lots, 2);
}

//==================================================================
// OPEN BUY
//==================================================================

void OpenBuy()
{
   double ask =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_ASK
      );

   double atr = GetATR();

   if(atr <= 0)
      return;

   double stopDistance =
      atr * ATRMultiplier;

   double sl =
      ask - stopDistance;

   double tp =
      ask + stopDistance * RiskReward;

   int digits =
      (int)SymbolInfoInteger(
         _Symbol,
         SYMBOL_DIGITS
      );

   sl =
      NormalizeDouble(sl, digits);

   tp =
      NormalizeDouble(tp, digits);

   double lot =
      CalculateLotSize(stopDistance);

   Print(
      "BUY: lot=", lot,
      " entry=", ask,
      " SL=", sl,
      " TP=", tp
   );

   bool result =
      trade.Buy(
         lot,
         _Symbol,
         0,
         sl,
         tp,
         "PriceAction BUY"
      );

   if(!result)
   {
      Print(
         "BUY request failed. Retcode=",
         trade.ResultRetcode(),
         " Description=",
         trade.ResultRetcodeDescription()
      );
   }
   else
   {
      Print(
         "BUY request sent. Retcode=",
         trade.ResultRetcode(),
         " Deal=",
         trade.ResultDeal()
      );
   }
}

//==================================================================
// OPEN SELL
//==================================================================

void OpenSell()
{
   double bid =
      SymbolInfoDouble(
         _Symbol,
         SYMBOL_BID
      );

   double atr = GetATR();

   if(atr <= 0)
      return;

   double stopDistance =
      atr * ATRMultiplier;

   double sl =
      bid + stopDistance;

   double tp =
      bid - stopDistance * RiskReward;

   int digits =
      (int)SymbolInfoInteger(
         _Symbol,
         SYMBOL_DIGITS
      );

   sl =
      NormalizeDouble(sl, digits);

   tp =
      NormalizeDouble(tp, digits);

   double lot =
      CalculateLotSize(stopDistance);

   Print(
      "SELL: lot=", lot,
      " entry=", bid,
      " SL=", sl,
      " TP=", tp
   );

   bool result =
      trade.Sell(
         lot,
         _Symbol,
         0,
         sl,
         tp,
         "PriceAction SELL"
      );

   if(!result)
   {
      Print(
         "SELL request failed. Retcode=",
         trade.ResultRetcode(),
         " Description=",
         trade.ResultRetcodeDescription()
      );
   }
   else
   {
      Print(
         "SELL request sent. Retcode=",
         trade.ResultRetcode(),
         " Deal=",
         trade.ResultDeal()
      );
   }
}

//+------------------------------------------------------------------+
//| END                                                              |
//+------------------------------------------------------------------+
