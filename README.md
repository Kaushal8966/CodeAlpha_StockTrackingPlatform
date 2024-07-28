# CodeAlpha_StockTrackingPlatform
import java.util.HashMap;
import java.util.Map;
import java.util.Random;
import java.util.Scanner;

// Market class to handle stock prices
class Market {
    private Map<String, Double> stocks;
    private Random random;

    public Market() {
        stocks = new HashMap<>();
        stocks.put("AAPL", 150.00);
        stocks.put("GOOGL", 2800.00);
        stocks.put("AMZN", 3500.00);
        stocks.put("MSFT", 300.00);
        stocks.put("TSLA", 700.00);
        random = new Random();
    }

    public void updatePrices() {
        for (String stock : stocks.keySet()) {
            double change = random.nextDouble() * 20 - 10;
            stocks.put(stock, stocks.get(stock) + change);
        }
    }

    public Double getPrice(String stock) {
        return stocks.get(stock);
    }

    public Map<String, Double> getStocks() {
        return stocks;
    }
}

// Portfolio class to manage user holdings
class Portfolio {
    private Map<String, Integer> holdings;
    private double cash;

    public Portfolio() {
        holdings = new HashMap<>();
        cash = 10000.00; // Starting cash
    }

    public boolean buyStock(String stock, int quantity, double price) {
        double cost = price * quantity;
        if (cash >= cost) {
            cash -= cost;
            holdings.put(stock, holdings.getOrDefault(stock, 0) + quantity);
            return true;
        } else {
            return false;
        }
    }

    public boolean sellStock(String stock, int quantity, double price) {
        if (holdings.getOrDefault(stock, 0) >= quantity) {
            double revenue = price * quantity;
            cash += revenue;
            holdings.put(stock, holdings.get(stock) - quantity);
            if (holdings.get(stock) == 0) {
                holdings.remove(stock);
            }
            return true;
        } else {
            return false;
        }
    }

    public double getCash() {
        return cash;
    }

    public Map<String, Integer> getHoldings() {
        return holdings;
    }

    public double getValue(Market market) {
        double totalValue = cash;
        for (String stock : holdings.keySet()) {
            totalValue += holdings.get(stock) * market.getPrice(stock);
        }
        return totalValue;
    }
}

// Main class to run the trading platform
public class StockTradingPlatform {
    public static void main(String[] args) {
        Market market = new Market();
        Portfolio portfolio = new Portfolio();
        Scanner scanner = new Scanner(System.in);

        while (true) {
            market.updatePrices();
            System.out.println("\nCurrent Market Prices:");
            for (Map.Entry<String, Double> entry : market.getStocks().entrySet()) {
                System.out.printf("%s: $%.2f%n", entry.getKey(), entry.getValue());
            }

            System.out.printf("\nPortfolio Value: $%.2f%n", portfolio.getValue(market));
            System.out.printf("Cash: $%.2f%n", portfolio.getCash());
            System.out.println("Holdings:");
            for (Map.Entry<String, Integer> entry : portfolio.getHoldings().entrySet()) {
                System.out.printf("%s: %d shares%n", entry.getKey(), entry.getValue());
            }

            System.out.print("\nDo you want to [b]uy, [s]ell, or [q]uit? ");
            String action = scanner.next().toLowerCase();
            if (action.equals("q")) {
                break;
            } else if (action.equals("b") || action.equals("s")) {
                System.out.print("Enter the stock symbol: ");
                String stock = scanner.next().toUpperCase();
                System.out.print("Enter the quantity: ");
                int quantity = scanner.nextInt();
                Double price = market.getPrice(stock);
                if (price == null) {
                    System.out.println("Invalid stock symbol.");
                    continue;
                }

                if (action.equals("b")) {
                    if (portfolio.buyStock(stock, quantity, price)) {
                        System.out.println("Purchase successful.");
                    } else {
                        System.out.println("Insufficient cash.");
                    }
                } else if (action.equals("s")) {
                    if (portfolio.sellStock(stock, quantity, price)) {
                        System.out.println("Sale successful.");
                    } else {
                        System.out.println("Insufficient shares.");
                    }
                }
            }
        }

        scanner.close();
    }
}
