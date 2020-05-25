data = readtable('sp500.xlsx');
sp = data.Close;
dates = data.Date;
Returns = tick2ret(sp);
Returns = Returns(2:end);
dates = datetime(dates, 'InputFormat', 'dd-MM-yyyy');
 
DateReturns = dates(2:end);
SampleSize = length(Returns);
 
TestWindowStart      = find(year(DateReturns)==1992,1);
TestWindow           = TestWindowStart : SampleSize;
EstimationWindowSize = 250;
pVaR = [0.05 0.01];
 
Historical95 = zeros(length(TestWindow),1);
Historical99 = zeros(length(TestWindow),1);
 
for t = TestWindow
    i = t - TestWindowStart + 1;
    EstimationWindow = t-EstimationWindowSize:t-1;
    X = Returns(EstimationWindow);
    Historical95(i) = -quantile(X,pVaR(1));
    Historical99(i) = -quantile(X,pVaR(2));
end
figure;
hold on
newcolors = {'#dd820d','#ff0101'};
colororder(newcolors)
plot(DateReturns(TestWindow),Historical95, DateReturns(TestWindow), Historical99, 'Linewidth', 1.5)
grid on
ylabel('VaR')
xlabel('Date')
legend({'95% Confidence Level','99% Confidence Level'},'Location','Best')
title('VaR Estimation Using the Historical Simulation Method')

ReturnsTest = Returns(TestWindow);
DatesTest   = DateReturns(TestWindow);
figure;

grid on
hold on
newcolors = {'#6517d1','#dd820d','#ff0101'};
colororder(newcolors)
p = plot(DatesTest, ReturnsTest, DatesTest, -Historical95, DatesTest, -Historical99)
p(1).LineWidth = 0.5;
p(2).LineWidth = 1.5;
p(3).LineWidth = 1.5;
hold off
ylabel('VaR')
xlabel('Date')
legend({'Returns','Historical95', 'Historical99'},'Location','Best')
title('Comparison of returns and VaR at 95% & 99% for different models')
 
 
ZoomInd   = (DatesTest >= datestr('01-Aug-2007','local')) & (DatesTest <= datestr('31-Dec-2007','local'));
VaRData   = [-Historical95(ZoomInd) -Historical99(ZoomInd)];
VaRFormat = {'--','-.'};
D = DatesTest(ZoomInd);
R = ReturnsTest(ZoomInd);
H95 = Historical95(ZoomInd);
H99 = Historical99(ZoomInd);
IndHS95   = (R < -H95);
IndHS99   = (R < -H99);
figure;
s = bar(D,R,0.85,'FaceColor',[0.7216    0.5804    0.8784]);
s.EdgeColor = 'none';
hold on
for i = 1 : size(VaRData,2)
     stairs(D-0.5,VaRData(:,i),VaRFormat{i});
 end
 ylabel('VaR')
 xlabel('Date')
 legend({'Returns','Historical 95', 'Historical 99'},'Location','Best','AutoUpdate','Off')
 title('95% & 99% VaR violations for different models')
 newcolors = {'#021f93','#dd820d','#ff0101','#038385'};
 colororder(newcolors)
 p1 = plot(D(IndHS95),-H95(IndHS95),'o',D(IndHS99),-H99(IndHS99),'o','MarkerSize',8)
 p1(1).LineWidth = 1.5;
 p1(2).LineWidth = 1.5;
 xlim([D(1)-1, D(end)+1])
 hold off;
 
 
vbt = varbacktest(ReturnsTest,[Historical95 Historical99],'PortfolioID','S&P','VaRID',...
    {'Historical95','Historical99'},'VaRLevel',[0.95 0.99]);
summary(vbt)
runtests(vbt)


Ind2007 = (year(DatesTest) == 2007);
vbt2007 = varbacktest(ReturnsTest(Ind2007),[Historical95(Ind2007),Historical99(Ind2007)],...
   'PortfolioID','S&P, 2007','VaRID',{'Historical95','Historical99'},'VaRLevel',[0.95 0.99]);
runtests(vbt2007)

cci(vbt2007)

tbfi(vbt2007)
