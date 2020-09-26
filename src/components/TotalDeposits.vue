<template>
	<div>
		<div class='window white'>
			<div class='chartoptions'>
				<input id='showbars' type='checkbox' v-model='showbars'>
				<label for='showbars'>Show bars</label>

				<input id='showline' type='checkbox' v-model='showline'>
				<label for='showline'>Show line</label>
			</div>
			<highcharts :constructor-type="'stockChart'" :options="chartdata" ref='highcharts'></highcharts>
		</div>

		<div class='window white'>
			<highcharts :constructor-type="'stockChart'" :options='volumeperpooldata' ref='volumeperpoolcharts'></highcharts>
		</div>

		<div class='window white'>
			<highcharts :constructor-type="'stockChart'" :options='liquidityutilizationdata' ref='liquidityutilizationcharts'></highcharts>
		</div>

		<div class='window white'>
			<highcharts :constructor-type="'stockChart'" :options='coinchartdata' ref='coincharts'></highcharts>
		</div>

		<div class='window white'>
			<highcharts :options="piechartdata" ref='piecharts'></highcharts>
		</div>

		<div class='window white'>
			<highcharts :options="piechartdatacoins" ref='piecoinscharts'></highcharts>
		</div>

		<div class='window white' v-show='totalDeposits > 0'>
			<highcharts :options="mypiechartdata" ref='mypiecharts'></highcharts>
		</div>
	</div>
</template>

<script>
	import Vue from 'vue'

	import Highcharts from 'highcharts'
	import HC_exporting from 'highcharts/modules/exporting';
	import HC_exporting_data from 'highcharts/modules/export-data';
	HC_exporting(Highcharts);
	HC_exporting_data(Highcharts)

	import {Chart} from 'highcharts-vue'
	import stockInit from 'highcharts/modules/stock'
	stockInit(Highcharts)

	Highcharts.setOptions({
		lang: {
			loading: '',
		}
	})

	import * as volumeStore from './common/volumeStore'
	import allabis from '../allabis'
	import * as helpers from '../utils/helpers'

	import { contract } from '../contract'

	import * as priceStore from './common/priceStore'

	export default {
		components: {
			highcharts: Chart,
		},
		data() {
			return {
				chartdata: {
				chart: {
					panning: true,
					zoomType: 'x',
			        panKey: 'ctrl',
			        type: 'column',
			        height: 600,
				},
		        title: {
		        	text: 'Total USD Deposits',
		        },
                rangeSelector: {
		            selected: 1
		        },
		        plotOptions: {
					series: {
						dataGrouping: {
						  forced: true,
						  units: [
						    ['day', [1]]
						  ]
						},
						point: {
							events: {
								click: (function(self) {
									return function() {
										let index = this.dataGroup ? this.dataGroup.start : this.index
										console.log(this, index, "INDEX")
									}
								})(this)
							}
						},
					},
					column: {
						stacking: 'normal',
						dataLabels: {
							enabled: false
						}
					},
				},
		        exporting: {
					buttons: {
						contextButton: {
							menuItems: ["printChart",
					                    "separator",
					                    "downloadPNG",
					                    "downloadJPEG",
					                    "downloadPDF",
					                    "downloadSVG",
					                    "separator",
					                    "downloadCSV",
					                    "downloadXLS",
					                    //"viewData",
					                    "openInCloud"]
						}
					}
				},
	            yAxis: {
	            	opposite: false,
	            	title: {
	            		text: 'Total deposits',
	            		style: {
	            			color: 'black'
	            		},
	            	},
            		stackLabels: {
			            enabled: false,
			            style: {
			                fontWeight: 'bold',
			                color: ( // theme
			                    Highcharts.defaultOptions.title.style &&
			                    Highcharts.defaultOptions.title.style.color
			                ) || 'gray'
			            }
			        },
	            	tickPixelInterval: 10,
	            },
	            xAxis: {
	            	labels: {
		            	style: {
		            		color: 'black'
		            	}
	            	},
	            },
		        series: [],
		        tooltip: {
	                valueDecimals: 5,
	                pointFormatter() {
	                	return `<span style="color:${this.color}">●</span> ${this.series.name}: <b>${this.y.toFixed(2)}</b><br/>`
	                },
	            },
	            legend: {
	            	enabled: true,
	            },
			},
			liquidityutilizationdata: {
				chart: {
					panning: true,
					zoomType: 'x',
			        panKey: 'ctrl',
			        type: 'column',
			        height: 600,
				},
		        title: {
		        	text: 'Liquidity utilization %',
		        },
                rangeSelector: {
		            selected: 1
		        },
		        plotOptions: {
					series: {
						dataGrouping: {
						  forced: true,
						  units: [
						    ['day', [1]]
						  ]
						},
						point: {
							events: {
								click: (function(self) {
									return function() {
										let index = this.dataGroup ? this.dataGroup.start : this.index
										console.log(this, index, "INDEX")
									}
								})(this)
							}
						},
					},
					column: {
						stacking: 'normal',
						dataLabels: {
							enabled: false
						}
					},
				},
		        exporting: {
					buttons: {
						contextButton: {
							menuItems: ["printChart",
					                    "separator",
					                    "downloadPNG",
					                    "downloadJPEG",
					                    "downloadPDF",
					                    "downloadSVG",
					                    "separator",
					                    "downloadCSV",
					                    "downloadXLS",
					                    //"viewData",
					                    "openInCloud"]
						}
					}
				},
	            yAxis: {
	            	opposite: false,
	            	title: {
	            		text: 'Liquidity utilization %',
	            		style: {
	            			color: 'black'
	            		},
	            	},
            		stackLabels: {
			            enabled: false,
			            style: {
			                fontWeight: 'bold',
			                color: ( // theme
			                    Highcharts.defaultOptions.title.style &&
			                    Highcharts.defaultOptions.title.style.color
			                ) || 'gray'
			            }
			        },
	            	tickPixelInterval: 10,
	            },
	            xAxis: {
	            	labels: {
		            	style: {
		            		color: 'black'
		            	}
	            	},
	            },
		        series: [],
		        tooltip: {
	                valueDecimals: 5,
	                pointFormatter() {
	                	return `<span style="color:${this.color}">●</span> ${this.series.name}: <b>${this.y.toFixed(2)}%</b><br/>`
	                },
	            },
	            legend: {
	            	enabled: true,
	            },
			},
			volumeperpooldata: {
				chart: {
					panning: true,
					zoomType: 'x',
			        panKey: 'ctrl',
			        type: 'column',
			        height: 600,
				},
		        title: {
		        	text: 'Total volume per pool in USD',
		        },
                rangeSelector: {
		            selected: 1
		        },
		        plotOptions: {
					series: {
						dataGrouping: {
						  forced: true,
						  units: [
						    ['day', [1]]
						  ]
						},
						point: {
							events: {
								click: (function(self) {
									return function() {
										let index = this.dataGroup ? this.dataGroup.start : this.index
										console.log(this, index, "INDEX")
									}
								})(this)
							}
						},
					},
					column: {
						stacking: 'normal',
						dataLabels: {
							enabled: false
						}
					},
				},
		        exporting: {
					buttons: {
						contextButton: {
							menuItems: ["printChart",
					                    "separator",
					                    "downloadPNG",
					                    "downloadJPEG",
					                    "downloadPDF",
					                    "downloadSVG",
					                    "separator",
					                    "downloadCSV",
					                    "downloadXLS",
					                    //"viewData",
					                    "openInCloud"]
						}
					}
				},
	            yAxis: {
	            	opposite: false,
	            	title: {
	            		text: 'Total volume per pool in USD',
	            		style: {
	            			color: 'black'
	            		},
	            	},
            		stackLabels: {
			            enabled: false,
			            style: {
			                fontWeight: 'bold',
			                color: ( // theme
			                    Highcharts.defaultOptions.title.style &&
			                    Highcharts.defaultOptions.title.style.color
			                ) || 'gray'
			            }
			        },
	            	tickPixelInterval: 10,
	            },
	            xAxis: {
	            	labels: {
		            	style: {
		            		color: 'black'
		            	}
	            	},
	            },
		        series: [],
		        tooltip: {
	                valueDecimals: 5,
	                pointFormatter() {
	                	return `<span style="color:${this.color}">●</span> ${this.series.name}: <b>${this.y.toFixed(2)}</b><br/>`
	                },
	            },
	            legend: {
	            	enabled: true,
	            },
			},
			piechartdata: {
				chart: {
			        plotBackgroundColor: null,
			        plotBorderWidth: null,
			        plotShadow: false,
			        type: 'pie'
			    },
			    title: {
			        text: 'Pool USD % holdings'
			    },
			    tooltip: {
			        pointFormat: '{series.name}: <b>{point.percentage:.1f}%</b>'
			    },
			    accessibility: {
			        point: {
			            valueSuffix: '%'
			        }
			    },
			    plotOptions: {
			        pie: {
			            allowPointSelect: true,
			            cursor: 'pointer',
			            dataLabels: {
			                enabled: true,
			                formatter: (function(self) {
			                	return function(point) {
			                		return `<b>${this.key}</b>:
			                		${helpers.formatNumber(self.allPools[this.key], 0)}$
			                		(${this.percentage.toFixed(2)}%)`
			                	}
			                })(this),
			            }
			        }
			    },
			    series: [],
			},
			piechartdatacoins: {
				chart: {
			        plotBackgroundColor: null,
			        plotBorderWidth: null,
			        plotShadow: false,
			        type: 'pie'
			    },
			    title: {
			        text: 'Pool coin % holdings'
			    },
			    tooltip: {
			        pointFormat: '{series.name}: <b>{point.percentage:.1f}%</b>'
			    },
			    accessibility: {
			        point: {
			            valueSuffix: '%'
			        }
			    },
			    plotOptions: {
			        pie: {
			            allowPointSelect: true,
			            cursor: 'pointer',
			            dataLabels: {
			                enabled: true,
			                formatter: (function(self) {
			                	return function(point) {
			                		return `<b>${this.key}</b>:
			                		${helpers.formatNumber(self.allCoins[this.key], 0)}$
			                		(${this.percentage.toFixed(2)}%)`
			                	}
			                })(this),
			            }
			        }
			    },
			    series: [],
			},
			mypiechartdata: {
				chart: {
			        plotBackgroundColor: null,
			        plotBorderWidth: null,
			        plotShadow: false,
			        type: 'pie'
			    },
			    title: {
			        text: 'My USD % holdings'
			    },
			    tooltip: {
			        pointFormat: '{series.name}: <b>{point.percentage:.1f}%</b>'
			    },
			    accessibility: {
			        point: {
			            valueSuffix: '%'
			        }
			    },
			    plotOptions: {
			        pie: {
			            allowPointSelect: true,
			            cursor: 'pointer',
			            dataLabels: {
			                enabled: true,
			                formatter: (function(self) {
			                	return function(point) {
			                		return `<b>${this.key}</b>:
			                		${helpers.formatNumber(self.balances[this.key == 'susd' ? 'susdv2' : this.key],0)}$
			                		(${this.percentage.toFixed(2)}%)`
			                	}
			                })(this),
			                filter: {
			                    property: 'percentage',
			                    operator: '>',
			                    value: 0
			                },
			            }
			        }
			    },
			    series: [],
			},
			coinchartdata: {
				chart: {
					panning: true,
					zoomType: 'x',
			        panKey: 'ctrl',
			        type: 'column',
			        height: 600,
				},
		        title: {
		        	text: 'Coin USD Deposits',
		        },
                rangeSelector: {
		            selected: 1
		        },
		        plotOptions: {
					series: {
						dataGrouping: {
						  forced: true,
						  units: [
						    ['day', [1]]
						  ]
						},
						point: {
							events: {
								click: (function(self) {
									return function() {
										let index = this.dataGroup ? this.dataGroup.start : this.index
										console.log(this, index, "INDEX")
									}
								})(this)
							}
						},
					},
					column: {
						stacking: 'normal',
						dataLabels: {
							enabled: false
						}
					},
				},
		        exporting: {
					buttons: {
						contextButton: {
							menuItems: ["printChart",
					                    "separator",
					                    "downloadPNG",
					                    "downloadJPEG",
					                    "downloadPDF",
					                    "downloadSVG",
					                    "separator",
					                    "downloadCSV",
					                    "downloadXLS",
					                    //"viewData",
					                    "openInCloud"]
						}
					}
				},
	            yAxis: {
	            	opposite: false,
	            	title: {
	            		text: 'Total coin deposits',
	            		style: {
	            			color: 'black'
	            		},
	            	},
            		stackLabels: {
			            enabled: false,
			            style: {
			                fontWeight: 'bold',
			                color: ( // theme
			                    Highcharts.defaultOptions.title.style &&
			                    Highcharts.defaultOptions.title.style.color
			                ) || 'gray'
			            }
			        },
	            	tickPixelInterval: 10,
	            },
	            xAxis: {
	            	labels: {
		            	style: {
		            		color: 'black'
		            	}
	            	},
	            },
		        series: [],
		        tooltip: {
	                valueDecimals: 5,
	                pointFormatter() {
	                	return `<span style="color:${this.color}">●</span> ${this.series.name}: <b>${this.y.toFixed(2)}</b><br/>`
	                },
	            },
	            legend: {
	            	enabled: true,
	            },
			},
			chart: null,
			piechart: null,
			piecoinschart: null,
			mypiechart: null,
			coinchart: null,
			showbars: true,
			showline: true,
			allPools: null,
			allCoins: null,
			balances: {
				compound: -1,
				usdt: -1,
				y: -1,
				busd: -1,
				susdv2: -1,
				pax: -1,
				tbtc: -1,
				ren: -1,
				sbtc: -1,
			},
			totalDeposits: 0,
		}
		},

		computed: {
			decodedGauges() {
				return [
				  "0x7ca5b0a2910B33e9759DC7dDB0413949071D7575",
				  "0xBC89cd85491d81C6AD2954E6d0362Ee29fCa8F53",
				  "0xFA712EE4788C042e2B7BB55E6cb8ec569C4530c1",
				  "0x69Fb7c45726cfE2baDeE8317005d3F94bE838840",
				  "0x64E3C23bfc40722d3B649844055F1D51c1ac041d",
				  "0xB1F2cdeC61db658F091671F5f199635aEF202CAC",
				  "0xA90996896660DEcC6E997655E065b23788857849",
				  "0x705350c4BcD35c9441419DdD5d2f097d7a55410F",
				  "0x4c18E409Dc8619bFb6a1cB56D114C3f592E0aE79",
				  "0xbFcF63294aD7105dEa65aA58F8AE5BE2D9d0952A",
				]
			},
			poolInfo() {
				return {
					compound: {
						swap: '0xA2B47E3D5c44877cca798226B7B8118F9BFb7A56',
						swap_token: '0x845838DF265Dcd2c412A1Dc9e959c7d08537f8a2',
						name: 'compound',
						gauge: '0x7ca5b0a2910B33e9759DC7dDB0413949071D7575',
					},
					usdt: {
						swap: '0x52EA46506B9CC5Ef470C5bf89f17Dc28bB35D85C',
						swap_token: '0x9fC689CCaDa600B6DF723D9E47D84d76664a1F23',
						name: 'usdt',
						gauge: '0xBC89cd85491d81C6AD2954E6d0362Ee29fCa8F53',
					},
					y: {
						swap: '0x45F783CCE6B7FF23B2ab2D70e416cdb7D6055f51',
						swap_token: '0xdF5e0e81Dff6FAF3A7e52BA697820c5e32D806A8',
						name: 'y',
						gauge: '0xFA712EE4788C042e2B7BB55E6cb8ec569C4530c1',
					},
					busd: {
						swap: '0x79a8C46DeA5aDa233ABaFFD40F3A0A2B1e5A4F27',
						swap_token: '0x3B3Ac5386837Dc563660FB6a0937DFAa5924333B',
						name: 'busd',
						gauge: '0x69Fb7c45726cfE2baDeE8317005d3F94bE838840',
					},
					susdv2: {
						swap: '0xA5407eAE9Ba41422680e2e00537571bcC53efBfD',
						swap_token: '0xC25a3A3b969415c80451098fa907EC722572917F',
						name: 'susdv2',
						gauge: '0xA90996896660DEcC6E997655E065b23788857849',
					},
					pax: {
						swap: '0x06364f10B501e868329afBc005b3492902d6C763',
						swap_token: '0xD905e2eaeBe188fc92179b6350807D8bd91Db0D8',
						name: 'pax',
						gauge: '0x64E3C23bfc40722d3B649844055F1D51c1ac041d',
					},
					ren: {
						swap: '0x93054188d876f558f4a66B2EF1d97d16eDf0895B',
						swap_token: '0x49849C98ae39Fff122806C06791Fa73784FB3675',
						name: 'ren',
						gauge: '0xB1F2cdeC61db658F091671F5f199635aEF202CAC',
					},
					sbtc: {
						swap: '0x7fC77b5c7614E1533320Ea6DDc2Eb61fa00A9714',
						swap_token: '0x075b1bb99792c9E1041bA13afEf80C91a1e70fB3',
						name: 'sbtc',
						gauge: '0x705350c4BcD35c9441419DdD5d2f097d7a55410F',
					},
					hbtc: {
						swap: '0x4CA9b3063Ec5866A4B82E437059D2C43d1be596F',
						swap_token: '0xb19059ebb43466C323583928285a49f558E572Fd',
						name: 'hbtc',
						gauge: '0x4c18E409Dc8619bFb6a1cB56D114C3f592E0aE79',
					},
					uSwap: {
						swap: '0x4571753311e37ddb44faa8fb78a6df9a6e3c6c0b',
						swap_token: '0x173f8ee61c0fe712cae2a2fc8d5c0ccdda57e6da',
						name: 'uSwap',
						gauge: '',
					},
					'3pool': {
						swap: '0xbebc44782c7db0a1a60cb6fe97d0b483032ff1c7',
						swap_token: '0x6c3F90f043a72FA612cbac8115EE7e52BDe6E490',
						name: '3pool',
						gauge: '0xbFcF63294aD7105dEa65aA58F8AE5BE2D9d0952A',
					},
				}
			},
		},

		watch: {
			showbars(val) {
				if(val == false && !this.showline) return;
				let toggle = 'show'
				if(!val) {
					toggle = 'hide'
				}
				for(let i = 0; i < this.chart.series.length - 2; i++) {
					this.chart.series[i][toggle]()
				}
			},
			showline(val) {
				if(val == false && !this.showbars) return
				let toggle = 'show'
				if(!val) toggle = 'hide'
				this.chart.series[this.chart.series.length - 2][toggle]()
			},
		},

		created() {
			this.$watch(() => contract.multicall && contract.default_account, (val, oldval) => {
				if(val && val.toLowerCase() == oldval && oldval.toLowerCase()) return;
				if(val) this.showBalances()
			}, {
				immediate: true,
			})
		},

		async mounted() {
			this.chart = this.$refs.highcharts.chart;
			this.piechart = this.$refs.piecharts.chart;
			this.piecoinschart = this.$refs.piecoinscharts.chart;
			this.mypiechart = this.$refs.mypiecharts.chart;
			this.coinchart = this.$refs.coincharts.chart;
			this.liquidityutilizationchart = this.$refs.liquidityutilizationcharts.chart
			this.volumeperpoolchart = this.$refs.volumeperpoolcharts.chart
			this.chart.showLoading()
			this.piechart.showLoading()
			this.mypiechart.showLoading()
			this.coinchart.showLoading()
			this.liquidityutilizationchart.showLoading()
			this.volumeperpoolchart.showLoading()
			let pools = Object.keys(allabis).filter(pool => pool != 'susd' && pool != 'y' && pool != 'tbtc')
			await volumeStore.fetchVolumeData(pools, true, 1440)
			let data = volumeStore.state.volumeData[1440]
			let btcPrices = await helpers.retry(fetch(`https://api.coingecko.com/api/v3/coins/bitcoin/market_chart/range?vs_currency=usd&from=1589587198&to=${(Date.now() / 1000) | 0}`))
			btcPrices = await btcPrices.json()
			for(let btcPool of ['ren', 'sbtc', 'hbtc']) {
				data[btcPool] = data[btcPool].map(d => {
					d.balances = d.balances.map(bal => bal * volumeStore.findClosestPrice(d.timestamp, btcPrices.prices))
					// d.volume = Object.fromEntries(Object.entries(d.volume).map(([k, vol]) => {
					// 	console.log(vol, "THE VOL")
					// 	return [k, vol.map(v => v * volumeStore.findClosestPrice(d.timestamp, btcPrices.prices))]}
					// ))
					return d;
				})
			}
			data = Object.keys(data).reduce((obj, key) => {
				return {...obj, [key]: (new Array(Math.max(...Object.values(data).map(arr=>arr.length))-data[key].length).fill({})).concat(data[key])}
			}, {})

			console.log(data, "THE DATA")

			let volumes = {}
			let liquidityUtilizations = {}
			let volumeperpool = {}


			for(let [pool, points] of Object.entries(data)) {
				volumes[pool] = []
				liquidityUtilizations[pool] = []
				volumeperpool[pool] = []
				for(let point of points) {
					if(!point.timestamp) continue
					let coinBalances = point.balances.map((bal, i) => {
						return bal * point.rates[i] / 1e18 / allabis[pool == 'susd' ? 'susdv2' : pool].coin_precisions[i]
					})
					let volumeBalances = Object.entries(point.volume).map(([k, vol]) => {
						return vol[0] / allabis[pool == 'susd' ? 'susdv2' : pool].coin_precisions[k.split('-')[0]]
					})
					let totalBalances = coinBalances.reduce((a, b) => a + b, 0)
					let totalVolume = volumeBalances.reduce((a, b) => a + b, 0)
					volumes[pool].push([
						point.timestamp * 1000,
						totalBalances,
					])
					volumeperpool[pool].push([
						point.timestamp * 1000,
						totalVolume,
					])
					liquidityUtilizations[pool].push([
						point.timestamp * 1000,
						totalVolume * 100 / totalBalances,
					])
				}
			}



			for(let [pool, volume] of Object.entries(volumes)) {
				if(['tbtc'].includes(pool)) continue
				this.chart.addSeries({
					type: 'column',
					name: pool,
					data: volume,
				}, false, false)
			}

			for(let [pool, liquidityUtilization] of Object.entries(liquidityUtilizations)) {
				if(['tbtc'].includes(pool)) continue
				this.liquidityutilizationchart.addSeries({
					type: 'column',
					name: pool,
					data: liquidityUtilization,
				}, true, false)
			}

			for(let [pool, volume] of Object.entries(volumeperpool)) {
				if(['tbtc'].includes(pool)) continue
				this.volumeperpoolchart.addSeries({
					type: 'column',
					name: pool,
					data: volume,
				}, true, false)
			}

			this.liquidityutilizationchart.hideLoading()

			this.volumeperpoolchart.hideLoading()

			let coins = ['DAI', 'USDC', 'USDT', 'TUSD', 'BUSD', 'SUSD', 'PAX', 'renBTC', 'WBTC', 'sBTC', 'hBTC']
			let coinbalances = coins.reduce((a,b)=> (a[b]=[],a),{});


			for(let [pool, points] of Object.entries(data)) {
				if(pool == 'tbtc') continue
				for(let point of points) {
					if(!point.timestamp) continue
					let coinBalances = point.balances.map((bal, i) => {
						return bal * point.rates[i] / 1e18 / allabis[pool == 'susd' ? 'susdv2' : pool].coin_precisions[i]
					})
					for(let [i, balance] of coinBalances.entries()) {
						if(pool == 'busd' && i == 3) i = 4
						if(pool == 'susd' && i == 3) i = 5
						if(pool == 'pax' && i == 3) i = 6
						if(['ren', 'sbtc'].includes(pool)) i += 7
						if(pool == 'hbtc' && i == 0) i = 10
						if(pool == 'hbtc' && i == 1) i = 8
						let hasPoint = coinbalances[coins[i]].find(p => p[0] == point.timestamp*1000)
						if(hasPoint !== undefined) {
							hasPoint[1] += balance
						}
						else
							coinbalances[coins[i]].push([point.timestamp*1000, balance])
					}
				}
			}

			for(let [coin, balance] of Object.entries(coinbalances)) {
				balance.sort((a,b) => a[0] - b[0])
				this.coinchart.addSeries({
					type: 'column',
					name: coin,
					data: balance,
				}, false, false)
			}


			this.chart.hideLoading()
			this.chart.redraw()

			this.coinchart.hideLoading()
			this.coinchart.redraw()

			let balances = Object.keys(volumes).reduce((obj, key) => {
				return {...obj, [key]: (new Array(Math.max(...Object.values(volumes).map(arr=>arr.length))-volumes[key].length).fill({})).concat(volumes[key])}
			}, {})


			let line = []

			for(let [i, balance] of balances.compound.entries()) {
				let v = Object.values(balances).map(v => v[i]).filter(v=>v[0])
				line.push([v[0][0], v.map(v=>v[1]).reduce((a, b) => a + b, 0)])
			}

			this.chart.addSeries({
				type: 'line',
				name: `Total USD Deposits`,
				data: line,
			})

			this.allPools = Object.fromEntries(Object.entries(volumes).filter(([p, v]) => !['tbtc'].includes(p))
									.map(([p, v]) => [p, volumes[p][volumes[p].length-1][1]]))

			let latestDeposits = Object.keys(volumes).filter(p => !['tbtc'].includes(p)).map(p => volumes[p][volumes[p].length-1][1])

			let poolHoldings = latestDeposits.reduce((a, b) => a + b, 0)

			let poolPercentages = latestDeposits.map(poolDeposits => (poolDeposits / poolHoldings) * 100)

			poolPercentages = poolPercentages.map((percentage, i) => ({
				name: Object.keys(volumes).filter(p => !['tbtc'].includes(p))[i],
				y: poolPercentages[i],
			}))


			let highest = poolPercentages.map(data=>data.y).indexOf(Math.max(...poolPercentages.map(data => data.y)))
			poolPercentages[highest].sliced = true;
			poolPercentages[highest].selected = true;

			this.piechart.addSeries({
				name: 'Pool %',
				data: poolPercentages,
			}, true, false)

			this.piechart.hideLoading()

			this.allCoins = Object.fromEntries(Object.entries(coinbalances).filter(([p, v]) => !['tbtc'].includes(p))
									.map(([p, v]) => {
										//console.log(p, coinbalances[p])
										return [p, coinbalances[p][coinbalances[p].length-1][1]]
									}))

			latestDeposits = Object.keys(coinbalances).filter(p => !['tbtc'].includes(p.toLowerCase())).map(p => coinbalances[p][coinbalances[p].length-1][1])

			poolHoldings = latestDeposits.reduce((a, b) => a + b, 0)

			poolPercentages = latestDeposits.map(poolDeposits => (poolDeposits / poolHoldings) * 100)

			poolPercentages = poolPercentages.map((percentage, i) => ({
				name: Object.keys(coinbalances).filter(p => !['tbtc'].includes(p))[i],
				y: poolPercentages[i],
			}))


			highest = poolPercentages.map(data=>data.y).indexOf(Math.max(...poolPercentages.map(data => data.y)))
			poolPercentages[highest].sliced = true;
			poolPercentages[highest].selected = true;

			this.piecoinschart.addSeries({
				name: 'Coin %',
				data: poolPercentages,
			}, true, false)

			this.piecoinschart.hideLoading()

			this.chart.update({
				title: {
					text: `Total USD Deposits ${helpers.formatNumber(line[line.length-1][1], 0)}$`
				},
			})




		},

		methods: {
			async showBalances() {
				let pools = ['compound','usdt','y','busd','susdv2','pax','ren', 'sbtc', 'hbtc', 'uSwap', '3pool']
				if(!contract.default_account) return;
				contract.contracts.compound = contract;
				let calls = pools.flatMap(k => {
					return [
						//balanceOf(address)
						[allabis[k].token_address, '0x70a08231000000000000000000000000' + contract.default_account.slice(2)],
						//get_virtual_price
						[allabis[k].swap_address, "0xbb7b8b80"]
					]})
				calls.push([allabis.susdv2.sCurveRewards_address, '0x70a08231000000000000000000000000' + contract.default_account.slice(2)])
				calls.push([allabis.sbtc.sCurveRewards_address, '0x70a08231000000000000000000000000' + contract.default_account.slice(2)])
				calls.push([allabis.iearn.sCurveRewards_address, '0x70a08231000000000000000000000000' + contract.default_account.slice(2)])
				let callslen = calls.length
				calls.push(...this.decodedGauges.map(gauge => [gauge, '0x70a08231000000000000000000000000' + contract.default_account.slice(2)]))
				let aggcalls = await contract.multicall.methods.aggregate(calls).call()
				let decoded = aggcalls[1].map(hex => web3.eth.abi.decodeParameter('uint256', hex))
				let btcPrice = await helpers.retry(priceStore.getBTCPrice())
				//this.balances = []
				let decodedGaugeBalances = decoded.slice(callslen)
				decoded = decoded.slice(0, callslen)
				helpers.chunkArr(decoded, 2).slice(0,pools.length).map((v, i) => {
					let key = pools[i]
					Vue.set(this.balances, key, +v[0] * (+v[1]) / 1e36);
					if(['tbtc', 'ren', 'sbtc', 'hbtc'].includes(key)) Vue.set(this.balances, key, this.balances[key] * btcPrice)
				})
				let len = decoded.length
				Vue.set(this.balances, 'susdv2', this.balances.susdv2 + (+decoded[len-3] * decoded[9]) / 1e36)
				Vue.set(this.balances, 'sbtc', this.balances.sbtc + ((+decoded[len-2] * decoded[15]) / 1e36) * btcPrice)
				Vue.set(this.balances, 'y', this.balances.y + (+decoded[len-1] * decoded[5]) / 1e36)

				for(let [i, pool] of Object.values(this.poolInfo).entries()) {
					let callsGauges = calls.slice(callslen)
					let balance = +decodedGaugeBalances[callsGauges.findIndex(callGauge => callGauge[0] == pool.gauge)] * (decoded[i*2 + 1] / 1e18)
					if(['ren', 'sbtc', 'hbtc'].includes(pool.name))
						balance *= btcPrice
					Vue.set(this.balances, pool.name, this.balances[pool.name] + balance / 1e18)
				}

				let deposits = Object.fromEntries(Object.entries(this.balances).map(([k, v]) => [k, v > 0 ? v : 0]))
				this.totalDeposits = Object.values(deposits).reduce((a, b) => a + b, 0)
				if(this.totalDeposits > 0) {
					let depositPercentages = Object.keys(deposits).map((pool, i) => ({
						name: pool == 'susdv2' ? 'susd' : pool,
						y: deposits[pool] / this.totalDeposits,
					}))

					let highest = depositPercentages.map(data=>data.y).indexOf(Math.max(...depositPercentages.map(data => data.y)))
					depositPercentages[highest].sliced = true;
					depositPercentages[highest].selected = true;

					this.mypiechart.addSeries({
						name: 'My pool %',
						data: depositPercentages,
					}, true, false)
				}

				this.mypiechart.hideLoading()

			},
		},
	}
</script>

<style scoped>
	.chartoptions label:nth-of-type(2) {
		margin-left: 1em;
	}
	.chartoptions {
		margin-bottom: 1em;
	}
</style>
